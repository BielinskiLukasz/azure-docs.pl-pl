---
title: Rozwiązywanie problemów
description: Informacje dotyczące rozwiązywania problemów z renderowaniem zdalnym platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679987"
---
# <a name="troubleshoot"></a>Rozwiązywanie problemów

Na tej stronie wymieniono typowe problemy zakłócające zdalne renderowanie platformy Azure oraz sposoby ich rozwiązania.

## <a name="client-cant-connect-to-server"></a>Klient nie może połączyć się z serwerem

Upewnij się, że zapory (na urządzeniu, wewnątrz routerów itp.) nie blokują następujących portów:

* **50051 (TCP)** - wymagane dla połączenia początkowego (uzgadnianie HTTP)
* **8266 (TCP+UDP)** - wymagane do przesyłania danych
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - wymagane dla [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Błąd "Odłączony: VideoFormatNotAvailable"

Sprawdź, czy twój procesor graficzny obsługuje sprzętowe dekodowanie wideo. Zobacz [Development PC](../overview/system-requirements.md#development-pc).

Jeśli pracujesz na laptopie z dwoma procesorami graficznymi, możliwe jest, że procesor graficzny, na którego pracujesz domyślnie, nie zapewnia sprzętowej funkcji dekodowania wideo. Jeśli tak, spróbuj wymusić na aplikacji użycie innego procesora GPU. Jest to często możliwe w ustawieniach sterownika GPU.

## <a name="h265-codec-not-available"></a>Kodek H265 jest niedostępny

Istnieją dwa powody, dla których serwer może odmówić połączenia z **kodekiem niedostępny** błąd.

**Kodek H265 nie jest zainstalowany:**

Najpierw należy zainstalować **rozszerzenia wideo HEVC,** jak wspomniano w sekcji [Oprogramowanie](../overview/system-requirements.md#software) wymagań systemowych.

Jeśli nadal występują problemy, upewnij się, że karta graficzna obsługuje H265 i masz zainstalowany najnowszy sterownik graficzny. Informacje dotyczące specyficznych dla dostawcy można znaleźć w sekcji [Komputer deweloperów.](../overview/system-requirements.md#development-pc)

**Koder-dekodu jest zainstalowany, ale nie można go używać:**

Przyczyną tego problemu jest niepoprawne ustawienie zabezpieczeń bibliotek DLL. Ten problem nie objawia się podczas próby oglądania filmów zakodowanych z H265. Ponowna instalacja kodera-dekodu również nie rozwiązuje problemu. Zamiast tego wykonaj następujące kroki:

1. Otwieranie **programu PowerShell z prawami administratora** i uruchamianie

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    To polecenie powinno `InstallLocation` wyprowadzić kodek, coś w stylu:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Otwieranie tego folderu w Eksploratorze Windows
1. Powinien istnieć **podfolder x86** i **x64.** Kliknij prawym przyciskiem myszy jeden z folderów i wybierz **polecenie Właściwości**
    1. Wybierz kartę **Zabezpieczenia** i kliknij przycisk Ustawienia **zaawansowane**
    1. Kliknij **pozycję Zmień** dla **właściciela**
    1. Wpisz **administratorów** w polu tekstowym
    1. Kliknij **pozycję Sprawdź nazwy** i PRZYCISK **OK**
1. Powtórz powyższe czynności dla innego folderu
1. Powtórz również powyższe kroki na każdym pliku DLL wewnątrz obu folderów. Powinny istnieć cztery biblioteki DLL w ogóle.

Aby sprawdzić, czy ustawienia są teraz poprawne, wykonaj tę opcję dla każdej z czterech bibliotek DLL:

1. Wybierz **właściwości > > zabezpieczeń**
1. Przejdź przez listę wszystkich **grup / użytkowników** i upewnij się, że każdy z nich ma odczyt & **Wykonaj** prawo (znacznik wyboru w kolumnie **allow** musi być zaznaczone)

## <a name="low-video-quality"></a>Niska jakość wideo

Jakość wideo może być zagrożona przez jakość sieci lub brakujący kodek wideo H265.

* Zobacz kroki, aby [zidentyfikować problemy z siecią](#unstable-holograms).
* Zobacz [wymagania systemowe dotyczące](../overview/system-requirements.md#development-pc) instalowania najnowszego sterownika karty graficznej.

## <a name="black-screen-after-successful-model-loading"></a>Czarny ekran po pomyślnym załadowaniu modelu

Jeśli jesteś połączony ze środowiskom uruchomieniowym renderowania i pomyślnie załadowany model, ale zobacz tylko czarny ekran później, to może mieć kilka różnych przyczyn.

Przed wykonaniem bardziej szczegółowej analizy zalecamy przetestowanie następujących czynności:

* Czy kodek H265 jest zainstalowany? Chociaż powinien istnieć powrót do kodeka H264, widzieliśmy przypadki, w których ten rezerwowy nie działał poprawnie. Zobacz [wymagania systemowe dotyczące](../overview/system-requirements.md#development-pc) instalowania najnowszego sterownika karty graficznej.
* Korzystając z projektu Unity, zamknij Unity, usuń *bibliotekę* tymczasową i *foldery obj* w katalogu projektu i ponownie załaduj/skompiluj projekt. W niektórych przypadkach buforowane dane spowodowały, że próbka nie działała poprawnie bez wyraźnego powodu.

Jeśli te dwa kroki nie pomogły, należy dowiedzieć się, czy klatki wideo są odbierane przez klienta, czy nie. Można to zbadać programowo, jak wyjaśniono w rozdziale [kwerend wydajności po stronie serwera.](../overview/features/performance-queries.md) Element `FrameStatistics struct` członkowski wskazuje, ile klatek wideo zostało odebranych. Jeśli ta liczba jest większa niż 0 i wzrasta wraz z czasem, klient otrzymuje rzeczywiste klatki wideo z serwera. W związku z tym musi to być problem po stronie klienta.

### <a name="common-client-side-issues"></a>Typowe problemy po stronie klienta

**Model nie znajduje się wewnątrz widoku frustum:**

W wielu przypadkach model jest wyświetlany poprawnie, ale znajduje się poza frustum aparatu. Częstym powodem jest to, że model został wyeksportowany z daleko poza centrum obrotu, więc jest przycięty przez aparat daleko płaszczyzny przycinania. Pomaga programowo wysyłać zapytania do obwiedni modelu i wizualizować pole z Unity jako pole wiersza lub wydrukować jego wartości w dzienniku debugowania.

**Potok renderowania Unity nie zawiera haków renderowania:**

Narzędzia Azure Remote Rendering haki do potoku renderowania Unity do kompozycji ramki z wideo i wykonać ponowneprojection. Aby sprawdzić, czy te haki istnieją, otwórz menu *Okno > Analiza > Debuger ramki*. Włącz go i upewnij się, że `HolographicRemotingCallbackPass` istnieją dwa wpisy dla w potoku:

![Debuger ramki Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Kod Unity przy użyciu interfejsu API zdalnego renderowania nie jest kompilowany

Przełącz *typ kompilacji* rozwiązania Unity na **Debug**. Podczas testowania ARR w `UNITY_EDITOR` edytorze Unity zdefiniować jest dostępna tylko w kompilacjach "Debug". Należy zauważyć, że nie jest to związane z typem kompilacji używanym dla [wdrożonych aplikacji,](../quickstarts/deploy-to-hololens.md)gdzie należy preferować kompilacje "Release".

## <a name="unstable-holograms"></a>Niestabilne hologramy

W przypadku, gdy renderowane obiekty wydają się poruszać wraz z ruchami głowy, mogą wystąpić problemy z *ponownym renderowaniem późnego etapu* (LSR). Zapoznaj się z sekcją na [temat ponownegoprojection późnego etapu,](../overview/features/late-stage-reprojection.md) aby uzyskać wskazówki dotyczące podejścia do takiej sytuacji.

Innym powodem niestabilnych hologramów (chwiejność, wypaczenie, zdenerwowanie lub skakanie hologramów) może być słaba łączność sieciowa, w szczególności niewystarczająca przepustowość sieci lub zbyt duże opóźnienie. Dobrym wskaźnikiem jakości połączenia sieciowego jest wartość `ARRServiceStats.VideoFramesReused`statystyk [wydajności.](../overview/features/performance-queries.md) Ponownie użyczone klatki wskazują sytuacje, w których stara klatka wideo musiała być ponownie usuowana po stronie klienta, ponieważ nie była dostępna nowa klatka wideo — na przykład z powodu utraty pakietów lub z powodu różnic w opóźnieniu sieci. Jeśli `ARRServiceStats.VideoFramesReused` często jest większy niż zero, oznacza to problem z siecią.

Inną wartością, `ARRServiceStats.LatencyPoseToReceiveAvg`na co należy zwrócić uwagę, jest . Powinna ona być stale poniżej 100 ms. Jeśli widzisz wyższe wartości, oznacza to, że masz połączenie z centrum danych, które jest zbyt daleko.

Aby uzyskać listę potencjalnych środków zaradczych, zobacz [wytyczne dotyczące łączności sieciowej](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](../overview/system-requirements.md)
* [Wymagania dotyczące sieci](../reference/network-requirements.md)
