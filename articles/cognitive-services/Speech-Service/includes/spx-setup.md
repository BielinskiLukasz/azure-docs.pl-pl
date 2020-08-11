---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c92d6569e3c92d3bad3575599283c7796bd78225
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068625"
---
## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest subskrypcja usługi Azure Speech. Zapoznaj się z [przewodnikiem](../get-started.md#new-resource) dotyczącym tworzenia nowej subskrypcji, jeśli jeszcze jej nie masz.

## <a name="download-and-install"></a>Pobieranie i instalowanie

#### <a name="windows-install"></a>[Instalacja systemu Windows](#tab/windowsinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Windows:

1. Zainstaluj system [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) lub [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobrania i Wyodrębnij wymagany podkatalog ( `spx-net471` dla .NET Framework 4,7 lub `spx-netcore-win-x64` dla platformy .NET Core 3,0 na procesorze x64).

W wierszu polecenia Zmień katalog na tę lokalizację, a następnie wpisz polecenie, `spx` Aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

> [!NOTE]
> W systemie Windows interfejs wiersza polecenia mowy może wyświetlać tylko czcionki dostępne dla wiersza poleceń na komputerze lokalnym.
> [Terminal systemu Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) obsługuje wszystkie czcionki opracowane interaktywnie przez interfejs wiersza polecenia mowy.
> Jeśli dane wyjściowe są wyprowadzane do pliku, Edytor tekstu, taki jak Notatnik lub przeglądarka sieci Web, np. Microsoft Edge, może również wyświetlać wszystkie czcionki.

> [!NOTE]
> Program PowerShell nie sprawdza katalogu lokalnego podczas wyszukiwania polecenia. W programie PowerShell Zmień katalog na lokalizację `spx` i Wywołaj narzędzie, wprowadzając polecenie `.\spx` .
> Jeśli dodasz ten katalog do ścieżki, program PowerShell i wiersz polecenia systemu Windows znajdą się `spx` z dowolnego katalogu bez `.\` prefiksu.

#### <a name="linux-install"></a>[Instalacja systemu Linux](#tab/linuxinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Linux na PROCESORze x64:

1. Zainstaluj program [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobierania i Wyodrębnij `spx-netcore-30-linux-x64` go do nowego `~/spx` katalogu.
4. W terminalu wpisz następujące polecenia:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Wpisz `spx` , aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

#### <a name="docker-install"></a>[Instalacja platformy Docker](#tab/dockerinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w kontenerze platformy Docker:

1. Zainstaluj i uruchom program [Docker Desktop dla danej platformy](https://www.docker.com/get-started).
1. W nowym wierszu polecenia lub terminalu wpisz następujące polecenie:`docker pull msftspeech/spx`
1. Wpisz to polecenie. Należy zapoznać się z informacjami pomocy dla interfejsu wiersza polecenia usługi Speech:`docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Instalowanie katalogu w kontenerze

Narzędzie interfejsu wiersza polecenia mowy zapisuje ustawienia konfiguracji jako pliki i ładuje je podczas wykonywania dowolnego polecenia (z wyjątkiem poleceń pomocy).
W przypadku korzystania z interfejsu wiersza polecenia mowy w kontenerze platformy Docker należy zainstalować katalog lokalny z kontenera, dzięki czemu narzędzie może przechowywać lub wyszukiwać ustawienia konfiguracji, a także umożliwia odczytanie lub zapisanie plików wymaganych przez polecenie, takich jak pliki audio mowy.

W systemie Windows wpisz następujące polecenie, aby utworzyć lokalny katalog interfejsu wiersza polecenia mowy, którego można użyć w kontenerze:

`mkdir c:\spx-data`

Lub w systemie Linux lub Mac wpisz to polecenie w terminalu, aby utworzyć katalog i wyświetlić jego ścieżkę bezwzględną:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Ścieżka bezwzględna zostanie użyta podczas wywoływania interfejsu wiersza polecenia mowy.

### <a name="run-speech-cli-in-the-container"></a>Uruchamianie interfejsu wiersza polecenia mowy w kontenerze

W tej dokumentacji przedstawiono polecenie interfejsu wiersza `spx` polecenia mowy używane w instalacjach programów innych niż Docker.
Podczas wywoływania `spx` polecenia w kontenerze platformy Docker należy zainstalować katalog w kontenerze w systemie plików, w którym interfejs wiersza polecenia mowy może przechowywać i znajdować wartości konfiguracji oraz pliki odczytu i zapisu.
W systemie Windows polecenia będą wyglądać następująco:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

W systemie Linux lub Mac polecenia zaczną wyglądać podobnie do tego:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Zamień na `/ABSOLUTE_PATH` ścieżkę bezwzględną pokazaną przez `pwd` polecenie w powyższej sekcji.

Aby użyć `spx` polecenia zainstalowanego w kontenerze, należy zawsze wprowadzić pełne pokazane powyżej polecenie, a następnie parametry żądania.
Na przykład w systemie Windows to polecenie ustawia klucz:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Nie można użyć mikrofonu lub głośników komputera podczas uruchamiania interfejsu wiersza polecenia mowy w kontenerze platformy Docker.
> Aby korzystać z tych urządzeń, Przekaż pliki audio do i z interfejsu wiersza polecenia mowy do nagrywania/odtwarzania poza kontenerem platformy Docker.
> Narzędzie interfejsu wiersza polecenia mowy może uzyskać dostęp do katalogu lokalnego, który został skonfigurowany w powyższych krokach.

***

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z interfejsu wiersza polecenia mowy, musisz najpierw wprowadzić informacje o kluczu subskrypcji mowy i regionie. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .
