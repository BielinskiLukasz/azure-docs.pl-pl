---
title: Testowanie modułów programu Terraform na platformie Azure za pomocą struktury Terratest
description: Dowiedz się, jak używać struktury Terratest do testowania modułów programu Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, konto magazynu, azure, terratest, test jednostkowy, test integracyjny
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: fce70916cef96cee6496478503305e2bb1e73c09
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656346"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Testowanie modułów programu Terraform na platformie Azure za pomocą struktury Terratest

Moduły programu Terraform umożliwiają tworzenie składników wielokrotnego użytku, które można łączyć i testować. Moduły programu Terraform uwzględniają hermetyzację, co jest przydatne podczas implementowania procesów infrastruktury jako kodu.

Podczas tworzenia modułów programu Terraform ważne jest implementowanie kontroli jakości. Niestety, mało jest dostępnej dokumentacji wyjaśniającej, jak tworzyć testy jednostkowe i testy integracji w modułach programu Terraform. W tym samouczku przedstawiono infrastrukturę testowania i najlepsze rozwiązania, które zaadaptowaliśmy podczas tworzenia [modułów programu Terraform na platformie Azure](https://registry.terraform.io/browse?provider=azurerm).

Przyjrzeliśmy się wszystkim najpopularniejszym infrastrukturom testowania i wybraliśmy strukturę [Terratest](https://github.com/gruntwork-io/terratest) na potrzeby testowania naszych modułów programu Terraform. Struktura Terratest jest zaimplementowana jako biblioteka języka Go. Struktura Terratest udostępnia kolekcję funkcji pomocniczych i wzorców dla typowych zadań testowania infrastruktury, takich jak wykonywanie żądań HTTP i uzyskiwanie dostępu do konkretnej maszyny wirtualnej za pomocą protokołu SSH. Na poniższej liście opisano niektóre główne zalety używania struktury Terratest:

- **Udostępnia wygodne procedury pomocnicze do sprawdzania infrastruktury**. Ta funkcjonalność jest przydatna, gdy chcesz zweryfikować rzeczywistą infrastrukturę w rzeczywistym środowisku.
- **Struktura folderów jest przejrzyście zorganizowana**. Przypadki testowe są przejrzyście zorganizowane i zgodne ze [standardową strukturą folderów modułu programu Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Wszystkie przypadki testowe są pisane w języku Go**. Programu Terraform używają głównie deweloperzy języka Go. Jeśli jesteś deweloperem języka Go, nie musisz uczyć się innego języka programowania, aby używać struktury Terratest. Ponadto, jedyne zależności wymagane do uruchamiania przypadków testowych struktury Terratest to język Go i program Terraform.
- **Infrastruktura jest wysoce rozszerzalna**. Możesz rozszerzać dodatkowe funkcje oparte na strukturze Terratest, włącznie z funkcjami specyficznymi dla platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł praktyczny jest niezależny od platformy. Przykłady kodu używane w tym artykule można uruchamiać w systemie Windows, Linux lub MacOS. 

Przed rozpoczęciem zainstaluj następujące oprogramowanie:

- **Język programowania Go**: przypadki testowe Terraform są pisane w języku [Go](https://golang.org/dl/).
- **DEP**: [dep](https://github.com/golang/dep#installation) to narzędzie do zarządzania zależnościami dla języka Go.
- **Interfejs wiersza polecenia platformy Azure**: [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) to narzędzie wiersza polecenia umożliwiające zarządzanie zasobami platformy Azure. (Program Terraform obsługuje uwierzytelnianie na platformie Azure za pomocą jednostki usługi lub [interfejsu wiersza polecenia platformy Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)).
- **mage**: używamy [pliku wykonywalnego mage](https://github.com/magefile/mage/releases), aby przedstawić, jak można uprościć uruchamianie przypadków struktury Terratest. 

## <a name="create-a-static-webpage-module"></a>Tworzenie modułu statycznej strony internetowej

W tym samouczku utworzysz moduł programu Terraform, który aprowizuje statyczną stronę internetową, przekazując pojedynczy plik HTML do obiektu blob w usłudze Azure Storage. Ten moduł zapewnia użytkownikom z całego świata dostęp do strony internetowej za pośrednictwem adresu URL zwracanego przez moduł.

> [!NOTE]
> Utwórz wszystkie pliki, które opisano w tej sekcji, w swojej lokalizacji [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Najpierw utwórz nowy folder o nazwie `staticwebpage` w folderze `src` w lokalizacji GoPath. Ogólną strukturę folderów w tym samouczku przedstawiono w poniższym przykładzie. Ta sekcja koncentruje się głównie na plikach oznaczonych gwiazdką `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Moduł statycznej strony internetowej akceptuje trzy elementy wejściowe. Dane wejściowe są zadeklarowane w pliku `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Jak wspomniano we wcześniejszej części tego artykułu, ten moduł również generuje wyjściowy adres URL, który jest zadeklarowany w pliku `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Główna logika modułu aprowizuje cztery zasoby:
- **grupa zasobów**: nazwa grupy zasobów to element wejściowy `website_name` z dołączonym ciągiem `-staging-rg`.
- **konto magazynu**: nazwa konta magazynu to element wejściowy `website_name` z dołączonym ciągiem `data001`. Aby zastosować się do ograniczeń nazw konta magazynu, moduł usuwa wszystkie znaki specjalne i używa małych liter w całej nazwie konta magazynu.
- **ustalona nazwa kontenera**: kontener ma nazwę `wwwroot` i jest tworzony na koncie magazynu.
- **pojedynczy plik HTML**: plik HTML jest odczytywany z elementu wejściowego `html_path` i przekazywany do pliku `wwwroot/index.html`.

Logika modułu statycznej strony internetowej jest zaimplementowana w pliku `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test jednostkowy

Struktura Terratest jest przeznaczona do testów integracji. W tym celu struktura Terratest aprowizuje rzeczywiste zasoby w rzeczywistym środowisku. Czasami zadania testów integracji mogą stać się bardzo duże, zwłaszcza w przypadku dużej ilości zasobów do aprowizowania. Dobrym przykładem jest logika konwertująca nazwy konta magazynu, do której odwoływano się w poprzedniej sekcji. 

Jednak właściwie nie musimy aprowizować żadnych zasobów. Chcemy tylko upewnić się, że logika konwersji nazw jest poprawna. Dzięki elastyczności struktury Terratest możemy użyć testów jednostkowych. Testy jednostkowe to uruchamiane lokalnie przypadki testowe (chociaż jest wymagany dostęp do Internetu). Przypadki testów jednostkowych uruchamiają polecenia `terraform init` i `terraform plan`, aby przeanalizować dane wyjściowe polecenia `terraform plan`, oraz wyszukują wartości atrybutów do porównania.

W pozostałej części tej sekcji opisano, jak użyć struktury Terratest do zaimplementowania testu jednostkowego w celu upewnienia się, że logika używana do konwertowania nazw konta magazynu jest poprawna. Tylko pliki oznaczone gwiazdką `(*)` są interesujące.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Najpierw użyjemy pustego pliku HTML o nazwie `./test/fixtures/storage-account-name/empty.html` jako elementu zastępczego.

Plik `./test/fixtures/storage-account-name/main.tf` to szkielet przypadku testowego. Przyjmuje jeden element wejściowy `website_name`, który jest także elementem wejściowym testów jednostkowych. Logikę pokazano poniżej:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Głównym składnikiem jest implementacja testów jednostkowych w pliku `./test/storage_account_name_unit_test.go`.

Deweloperzy języka Go prawdopodobnie zauważą, że test jednostkowy jest zgodny z sygnaturą klasycznej funkcji testowej języka Go, ponieważ akceptuje argument typu `*testing.T`.

W treści testu jednostkowego mamy łącznie pięć przypadków zdefiniowanych w zmiennej `testCases` (element `key` jako dane wejściowe i element `value` jako oczekiwane dane wyjściowe). Dla każdego przypadku testu jednostkowego najpierw uruchamiamy polecenie `terraform init` dla folderu warunków początkowych testu (`./test/fixtures/storage-account-name/`). 

Następnie polecenie `terraform plan` z konkretnym przypadkiem testowym określonym jako dane wejściowe (zobacz definicję `website_name` w elemencie `tfOptions`) zapisuje wynik do pliku `./test/fixtures/storage-account-name/terraform.tfplan` (którego nie ma w ogólnej strukturze folderów).

Ten plik wyników jest analizowany i przekształcany w strukturę możliwą do odczytania przez kod przy użyciu oficjalnego analizatora planów programu Terraform.

Teraz spojrzymy na atrybuty, którymi jesteśmy zainteresowani (w tym przypadku to atrybut `name` elementu `azurerm_storage_account`) i porównamy wyniki z oczekiwanymi danymi wyjściowymi:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Aby uruchomić testy jednostkowe, wykonaj następujące kroki w wierszu polecenia:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Wynik testu w języku Go jest zwykle zwracany w ciągu około minuty.

### <a name="integration-test"></a>Test integracji

W przeciwieństwie do testów jednostkowych, testy integracji muszą aprowizować zasoby do rzeczywistego środowiska na potrzeby kompleksowej perspektywy. Struktura Terratest dobrze obsługuje tego typu zadania. 

Najlepsze rozwiązania dotyczące instalowania modułów programu Terraform obejmują instalację folderu `examples`. Folder `examples` zawiera kilka kompleksowych przykładów. Aby uniknąć pracy z rzeczywistymi danymi, dlaczego nie przetestować tych przykładów w formie testów integracji? W tej sekcji skupimy się na trzech plikach, które są oznaczone gwiazdką `(*)` w następującej strukturze folderów:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Zacznijmy od przykładów. Nowy przykładowy folder o nazwie `hello-world/` został utworzony w folderze `./examples/`. W tym miejscu udostępniamy prostą stronę HTML do przekazania: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Przykład dotyczący struktury Terraform `./examples/hello-world/main.tf` jest podobny do przedstawionego w teście jednostkowym. Jest jedna istotna różnica: przykład wyświetla też adres URL przekazanego kodu HTML jako stronę internetową o nazwie `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Ponownie używamy funkcji testu struktury Terratest i klasycznych funkcji testu języka Go w pliku testu integracji `./test/hello_world_example_test.go`.

W przeciwieństwie do testów jednostkowych, testy integracji tworzą rzeczywiste zasoby na platformie Azure. Dlatego należy uważać, aby uniknąć konfliktów nazw. (Należy zwrócić szczególną uwagę na niektóre globalnie unikatowe nazwy, takie jak nazwy konta magazynu). W związku z tym pierwszym krokiem logiki testowania jest wygenerowanie losowej wartości `websiteName` przy użyciu funkcji `UniqueId()` udostępnionej przez strukturę Terratest. Ta funkcja generuje losową nazwę zawierającą małe litery, wielkie litery lub cyfry. Element `tfOptions` powoduje, że wszystkie polecenia struktury Terraform dotyczą folderu `./examples/hello-world/`. Zapewnia też, że element `website_name` jest ustawiany na losową wartość `websiteName`.

Następnie po kolei są wykonywane funkcje `terraform init`, `terraform apply` i `terraform output`. Użyjemy kolejnej funkcji pomocniczej (`HttpGetWithCustomValidation()`) udostępnianej przez strukturę Terratest. Użycie tej funkcji pomocniczej pozwala upewnić się, że kod HTML został przekazany do wyjściowego adresu URL `homepage` zwróconego przez polecenie `terraform output`. Porównamy kod stanu HTTP GET z wartością `200` i wyszukamy pewne słowa kluczowe w zawartości HTML. Na koniec wykonanie funkcji `terraform destroy` jest zapewnione dzięki wykorzystaniu instrukcji `defer` języka Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Aby uruchomić testy integracji, wykonaj następujące kroki w wierszu polecenia:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Wynik testu w języku Go jest zwykle zwracany w ciągu około dwóch minut. Możesz także uruchomić oba testy jednostkowe oraz testy integracji, uruchamiając następujące polecenia:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Testy integracji trwają znacznie dłużej niż testy jednostkowe (dwie minuty dla jednego przypadku testowego integracji w porównaniu z jedną minutą dla pięciu przypadków testowych testów jednostkowych). Jednak do Ciebie należy decyzja, czy użyć testów jednostkowych, czy testów integracji w danym scenariuszu. Zwykle preferowane jest stosowanie testów jednostkowych dla skomplikowanej logiki przez użycie funkcji HCL programu Terraform. Testy integracji są zwykle stosowane w przypadku kompleksowej perspektywy użytkownika.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Użycie narzędzia mage do uproszczenia uruchamiania przypadków struktury Terratest 

Uruchamianie przypadków testowych w usłudze Azure Cloud Shell nie jest łatwe. Należy przechodzić do różnych katalogów i uruchamiać różne polecenia. Aby uniknąć używania usługi Cloud Shell, wprowadzimy system kompilacji w naszym projekcie. W tej sekcji użyjemy narzędzia mage systemu kompilacji języka Go, aby wykonać zadanie.

Jedyną rzeczą wymaganą przez narzędzie mage jest umieszczenie pliku `magefile.go` (oznaczonego znakiem `(+)` w poniższym przykładzie) w katalogu głównym projektu:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Oto przykład pliku `./magefile.go`. W tym skrypcie kompilacji napisanym w języku Go implementujemy pięć kroków kompilacji:
- `Clean`: ten krok usuwa wszystkie wygenerowane i tymczasowe pliki, które są generowane podczas wykonywania testów.
- `Format`: ten krok uruchamia polecenia `terraform fmt` i `go fmt` w celu sformatowania kodu bazowego.
- `Unit`: ten krok uruchamia wszystkie testy jednostkowe (z użyciem konwencji nazw funkcji `TestUT_*`) w folderze `./test/`.
- `Integration`: krok podobny do kroku `Unit`, ale zamiast testów jednostkowych są wykonywane testy integracji (`TestIT_*`).
- `Full`: ten krok uruchamia kolejno kroki `Clean`, `Format`, `Unit` i `Integration`.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Możesz użyć następujących poleceń, aby uruchomić pełny zestaw testów. Kod jest podobny do kroków uruchamiania używanych w poprzedniej sekcji. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Ostatnią pozycję wiersza polecenia możesz zastąpić dodatkowymi krokami narzędzia mage. Na przykład możesz użyć polecenia `mage unit` lub `mage clean`. Dobrym pomysłem jest osadzenie poleceń `dep` i polecenia `az login` w pliku magefile. Nie przedstawiono tu odpowiedniego kodu. 

Korzystając z narzędzia mage, można też współużytkować kroki przez użycie systemu pakietów języka Go. W takim przypadku można uprościć pliki magefile dla wszystkich modułów, odwołując się tylko do wspólnej implementacji i deklarując zależności (`mg.Deps()`).

**Opcjonalnie: można ustawić zmienne środowiskowe jednostki usługi pod kątem uruchamiania testów akceptacyjnych**
 
Zamiast uruchamiania polecenia `az login` przed testami, możesz przeprowadzić uwierzytelnianie platformy Azure, ustawiając zmienne środowiskowe jednostki usługi. Program Terraform publikuje [listę nazw zmiennych środowiskowych](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Tylko pierwsze cztery zmienne środowiskowe są wymagane). Program Terraform publikuje również szczegółowe instrukcje objaśniające sposób [uzyskiwania wartości tych zmiennych środowiskowych](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat struktury Terratest, zobacz [stronę struktury Terratest w witrynie GitHub](https://github.com/gruntwork-io/terratest).
* Aby uzyskać informacje o narzędziu mage, zobacz [stronę narzędzia mage w witrynie GitHub](https://github.com/magefile/mage) i [witrynę internetową narzędzia mage](https://magefile.org/).
