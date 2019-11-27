---
title: Wypychanie i ściąganie artefaktów OCI
description: Wypychanie i ściąganie artefaktów Open Container Initiative (OCI) przy użyciu prywatnego rejestru kontenerów na platformie Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.openlocfilehash: cb58a7ed51ae15d33ffdbb616c9b32ef03bcbfb7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456261"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Wypychanie i ściąganie artefaktu OCI przy użyciu usługi Azure Container Registry

Usługa Azure Container Registry umożliwia przechowywanie [artefaktów Open Container Initiative (OCI)](container-registry-image-formats.md#oci-artifacts) i zarządzanie nimi, a także obrazów kontenerów zgodnych z platformą Docker i Docker.

Aby zademonstrować tę możliwość, w tym artykule przedstawiono sposób użycia narzędzia [OCI rejestru as Storage (ORAS)](https://github.com/deislabs/oras) w celu wypchnięcia przykładowego artefaktu — pliku tekstowego — do usługi Azure Container Registry. Następnie należy ściągnąć artefakt z rejestru. Można zarządzać różnymi artefaktami OCI w rejestrze kontenerów platformy Azure przy użyciu różnych narzędzi wiersza polecenia odpowiednich dla każdego artefaktu.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Narzędzie ORAS** — Pobierz i zainstaluj bieżącą wersję ORAS dla danego systemu operacyjnego z [repozytorium GitHub](https://github.com/deislabs/oras/releases). Narzędzie zostanie wydane jako skompresowany plik plik tar (`.tar.gz`). Wyodrębnij i Zainstaluj plik przy użyciu standardowych procedur dla systemu operacyjnego.
* **Azure Active Directory jednostkę usługi (opcjonalnie)** — aby przeprowadzić uwierzytelnianie bezpośrednio przy użyciu ORAS, Utwórz [nazwę główną usługi](container-registry-auth-service-principal.md) , aby uzyskać dostęp do rejestru. Upewnij się, że jednostka usługi ma przypisaną rolę, taką jak AcrPush, dzięki czemu ma uprawnienia do wypychania i ściągania artefaktów.
* **Interfejs wiersza polecenia platformy Azure (opcjonalnie)** — aby użyć pojedynczej tożsamości, potrzebna jest lokalna instalacja interfejsu wiersza polecenia platformy Azure. Zalecana jest wersja 2.0.71 lub nowsza. Uruchom `az --version `, aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker (opcjonalnie)** — aby użyć pojedynczej tożsamości, należy również zainstalować lokalnie platformę Docker w celu uwierzytelnienia w rejestrze. Platforma Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows]lub [Linux][docker-linux] .


## <a name="sign-in-to-a-registry"></a>Logowanie do rejestru

W tej sekcji przedstawiono dwa sugerowane przepływy pracy do zalogowania do rejestru w zależności od użytej tożsamości. Wybierz metodę odpowiednią dla danego środowiska.

### <a name="sign-in-with-oras"></a>Zaloguj się przy użyciu ORAS

Korzystając z jednostki [usługi](container-registry-auth-service-principal.md) z prawami wypychania, uruchom polecenie `oras login`, aby zalogować się do rejestru przy użyciu identyfikatora aplikacji głównej usługi i hasła. Określ w pełni kwalifikowaną nazwę rejestru (wszystkie małe litery), w tym przypadku *myregistry.azurecr.IO*. Identyfikator aplikacji głównej usługi jest przekazywać w zmiennej środowiskowej `$SP_APP_ID`i hasłem w zmiennej `$SP_PASSWD`.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Aby odczytać hasło z stdin, użyj `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure

[Zaloguj](/cli/azure/authenticate-azure-cli) się do interfejsu wiersza polecenia platformy Azure przy użyciu swojej tożsamości, aby wypchnąć i ściągnąć artefakty z rejestru kontenerów.

Następnie do uzyskiwania dostępu do rejestru służy polecenie [ACR](/cli/azure/acr?view=azure-cli-latest#az-acr-login) interfejsu wiersza polecenia platformy Azure. Na przykład w celu uwierzytelnienia w rejestrze o nazwie Moje *Rejestr*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` używa klienta platformy Docker w celu ustawienia tokenu Azure Active Directory w pliku `docker.config`. Aby można było ukończyć indywidualny przepływ uwierzytelniania, należy zainstalować i uruchomić klienta platformy Docker.

## <a name="push-an-artifact"></a>Wypychanie artefaktu

Utwórz plik tekstowy w lokalnym roboczym katalogu roboczym z niektórym przykładowym tekstem. Na przykład w bash Shell:

```bash
echo "Here is an artifact!" > artifact.txt
```

Użyj polecenia `oras push`, aby wypchnąć ten plik tekstowy do rejestru. Poniższy przykład wypychanie przykładowego pliku tekstowego do repozytorium `samples/artifact`. Rejestr jest identyfikowany za pomocą w pełni kwalifikowanej nazwy rejestru *myregistry.azurecr.IO* (wszystkie małe litery). Artefakt jest otagowany `1.0`. Artefakt ma niezdefiniowany typ, domyślnie identyfikowany przez ciąg *typu nośnika* po nazwie pliku `artifact.txt`. Zobacz [artefakty OCI](https://github.com/opencontainers/artifacts) dla dodatkowych typów. 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

Dane wyjściowe dla pomyślnego wypychania są podobne do następujących:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Aby zarządzać artefaktami w rejestrze, jeśli używasz interfejsu wiersza polecenia platformy Azure, uruchom standardowe polecenia `az acr` do zarządzania obrazami. Na przykład Pobierz atrybuty artefaktu przy użyciu polecenia [AZ ACR Repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Dane wyjściowe będą podobne do następujących:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Ściąganie artefaktu

Uruchom `oras pull` polecenie, aby ściągnąć artefakt z rejestru.

Najpierw usuń plik tekstowy z lokalnego katalogu roboczego:

```bash
rm artifact.txt
```

Uruchom `oras pull`, aby ściągnąć artefakt i określić typ nośnika używany do wypchnięcia artefaktu:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Sprawdź, czy ściąganie zakończyło się pomyślnie:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Usuń artefakt (opcjonalnie)

Aby usunąć artefakt z usługi Azure Container Registry, użyj polecenia [AZ ACR Repository Delete][az-acr-repository-delete] . Poniższy przykład usuwa artefakt zapisany w tym miejscu:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [bibliotece ORAS](https://github.com/deislabs/oras/tree/master/docs), w tym o sposobie konfigurowania manifestu dla artefaktu
* Odwiedź repozytorium [artefaktów OCI](https://github.com/opencontainers/artifacts) , aby uzyskać informacje referencyjne o nowych typach artefaktów



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
