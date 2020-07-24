---
title: Samouczek — Inicjowanie obsługi wystąpienia chmurowego platformy Azure z Terraform
description: Inicjowanie obsługi administracyjnej wystąpienia chmurowego platformy Azure z Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 817a7c132657ba0ad8910a334b571f9d05481a0d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021226"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>Samouczek: Inicjowanie obsługi chmurowej usługi Azure wiosny za pomocą Terraform

W tym samouczku przedstawiono tworzenie wystąpienia chmury wiosennej platformy Azure przy użyciu Terraform. Procedury te przeprowadzą Cię przez proces tworzenia następujących zasobów:

> [!div class="checklist"]
> * Grupa zasobów
> * Wystąpienie chmury Azure wiosennej
> * Usługa Azure Storage dla Log Analytics

> [!NOTE]
> Aby uzyskać pomoc techniczną dla konkretnych Terraform, użyj jednego z kanałów pomocy technicznej społeczności HashiCorp do terraform:
>
> * Pytania, przypadki użycia i przydatne wzorce: [sekcja Terraform w portalu społeczności HashiCorp](https://discuss.hashicorp.com/c/terraform-core)
> * Pytania dotyczące dostawcy: [sekcja Terraform Providers The HashiCorp Community Portal](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-configuration-file"></a>Tworzenie pliku konfiguracji

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#use-azure-cloud-shell).

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfiguracja w tym kroku obejmuje modele zasobów platformy Azure, w tym grupę zasobów platformy Azure i wystąpienie chmury wiosennej na platformie Azure.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Zapisz plik (** &lt; Ctrl>S**) i wyjdź z edytora (** &lt; Ctrl>Q**).

## <a name="apply-the-configuration"></a>Zastosuj konfigurację

W tej sekcji użyjesz kilku poleceń Terraform, aby uruchomić konfigurację.

1. Polecenie [terraform init](https://www.terraform.io/docs/commands/init.html) inicjuje katalog roboczy. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform init
    ```

1. [Plan Terraform](https://www.terraform.io/docs/commands/plan.html) polecenia jest używany do sprawdzania poprawności składni konfiguracji. `-out`Parametr kieruje wyniki do pliku. Plik wyjściowy może być później używany do zastosowania konfiguracji. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Zastosowanie polecenia [Terraform](https://www.terraform.io/docs/commands/apply.html) służy do zastosowania konfiguracji. Polecenie Określa plik wyjściowy z poprzedniego kroku. To polecenie tworzy zasoby platformy Azure. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Aby sprawdzić wyniki w Azure Portal, przejdź do nowej grupy zasobów. Nowe **wystąpienie Azure Cosmos DB** zostanie wyświetlone w nowej grupie zasobów.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Aktualizowanie konfiguracji do dzienników konfiguracji i metryk

W tej sekcji pokazano, jak zaktualizować konfigurację, aby włączyć rejestrowanie i metryki dla chmury wiosennej platformy Azure za pomocą konta usługi Azure Storage.

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Dodaj następującą konfigurację na końcu pliku:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Zapisz plik (** &lt; Ctrl>S**) i wyjdź z edytora (** &lt; Ctrl>Q**).

1. Tak jak w poprzedniej sekcji, uruchom następujące polecenie, aby wprowadzić zmiany:

    ```bash
    terraform plan --out plan.out
    ```

1. Uruchom `terraform apply` polecenie, aby zastosować konfigurację.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule.

Uruchom polecenie [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) , aby usunąć zasoby platformy Azure utworzone w ramach tego samouczka:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj i skonfiguruj Terraform, aby udostępnić zasoby platformy Azure](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell).