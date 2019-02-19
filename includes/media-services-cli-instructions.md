---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: c13ba8ab42d949e5752bfefd9b792b14a5a5ce59
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56408215"
---
## <a name="cli-shell"></a>Interfejs wiersza polecenia powłoki

Zalecane jest użycie [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do wykonywania poleceń interfejsu wiersza polecenia. **Cloud Shell** jest bezpłatna, interaktywne powłoce, która służy do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Zapewnia elastyczność wyboru powłoki, która najlepiej pasuje do sposobu, w jaki pracujesz. Użytkownicy systemu Linux mogą wybrać korzystanie z powłoki Bash, zaś użytkownicy systemu Windows mogą wybrać środowisko PowerShell.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Login

Aby rozpocząć korzystanie z powłoki interfejsu wiersza polecenia (w chmurze lub lokalnie), uruchom `az login` do utworzenia połączenia z platformą Azure.

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie trzeba otworzyć stronę przeglądarki i postępuj zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do https://aka.ms/devicelogin w przeglądarce.

### <a name="specify-location-of-files"></a>Określ lokalizację plików

Wiele poleceń interfejsu wiersza polecenia Media Services umożliwia przekazanie parametru o nazwie pliku. Jeśli używasz **Cloud Shell**, możesz przekazać plik do usługi clouddrive (przy użyciu powłoki Bash lub programu PowerShell). 

![Przekazywanie plików]

Czy używasz lokalnej interfejsu wiersza polecenia lub **Cloud Shell**, należy określić ścieżkę pliku, zgodnie z systemu operacyjnego lub w usłudze Cloud Shell (powłoki Bash lub programu PowerShell), którego używasz. Poniżej znajduje się kilka przykładów:

Względna ścieżka do pliku (wszystkie OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Ścieżka bezwzględna do pliku w systemach operacyjnych Windows i Linux lub Mac

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`


[Przekazywanie plików]: ./media/media-services-cli/upload-download-files.png
