---
title: Łączenie programu Operations Manager z usługą Log Analytics | Microsoft Docs
description: Aby obsługiwać istniejącą inwestycję w programie System Center Operations Manager i korzystać z rozszerzonych możliwości usługi Log Analytics, można zintegrować program Operations Manager z obszarem roboczym.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8b061ad2926110e27524ededb5c33dffa5ea04b6
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635462"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Łączenie programu Operations Manager z usługą Log Analytics
Aby zachować istniejącą inwestycję w [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) i rozszerzone możliwości za pomocą usługi Log Analytics, Operations Manager można zintegrować z obszaru roboczego usługi Log Analytics. Umożliwi to wykorzystanie szans sprzedaży usługi Log Analytics przy ciągłym korzystaniu z programu Operations Manager do wykonywania następujących czynności:

* Monitorowanie kondycji usług IT za pomocą programu Operations Manager
* Utrzymywanie integracji z rozwiązaniami ITSM obsługującymi zarządzanie zdarzeniami i problemami
* Zarządzanie cyklem życia agentów wdrożonych lokalnie oraz na maszynach wirtualnych IaaS w chmurze publicznej monitorowanych za pomocą programu Operations Manager

Integracja z programem System Center Operations Manager stanowi dodatkową wartość w strategii operacji usługi przez wykorzystanie szybkości i wydajności usługi Log Analytics podczas zbierania, przechowywania i analizowania danych z programu Operations Manager. Usługa Log Analytics pomaga skorelować działania mające na celu zidentyfikowanie usterek i problemów oraz poznanie cykli obsługi procesu zarządzania istniejącymi problemami. Elastyczność aparatu wyszukiwania umożliwiającego zbadanie danych wydajności, zdarzeń i alertów wraz z bogatymi możliwościami wyeksponowania tych danych w przejrzysty sposób na pulpitach nawigacyjnych i w raportach demonstruje siłę współpracy usługi Log Analytics i programu Operations Manager.

Agenci raportujący w grupie zarządzania programu Operations Manager zbierają dane z serwerów, bazując na źródłach danych i rozwiązaniach usługi Log Analytics, które włączono w obszarze roboczym. W zależności od tego, które rozwiązania zostały włączone, dane z tych rozwiązań są albo przesyłane bezpośrednio z serwera zarządzania programu Operations Manager do usługi, albo, ze względu na ilość danych zgromadzonych na systemie zarządzanym przez agenta, są wysyłane bezpośrednio z agenta do usługi Log Analytics. Serwer zarządzania przekazuje dane bezpośrednio do usługi. Nie są one nigdy zapisywane w magazynie danych ani w operacyjnej bazie danych. Jeśli serwer zarządzania utraci łączność z usługą Log Analytics, umieszcza dane w lokalnej pamięci podręcznej do czasu ponownego nawiązania komunikacji w usługą Log Analytics. Jeśli serwer zarządzania przejdzie w tryb offline z powodu zaplanowanej konserwacji lub nieplanowanej awarii, inny serwer zarządzania w grupie zarządzania wznawia łączność z usługą Log Analytics.  

Na poniższym diagramie przedstawiono połączenie między serwerami zarządzania i agentami w grupie zarządzania programu System Center Operations Manager oraz usłudze Log Analytics wraz z informacją o kierunkach i portach.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Jeśli Twoje informatyczne zasady zabezpieczeń nie zezwalają na komputerach w sieci, aby nawiązać połączenie z Internetem, serwery zarządzania można skonfigurować do połączenia z bramą usługi Log Analytics w celu odbierania informacji o konfiguracji i wysyłania danych zebranych w zależności od rozwiązania włączone. Aby uzyskać więcej informacji oraz instrukcje dotyczące sposobu konfigurowania grupy zarządzania programu Operations Manager do komunikowania się za pośrednictwem bramy usługi Log Analytics do usługi Log Analytics, zobacz [łączenie komputerów z usługą Log Analytics przy użyciu bramy usługi Log Analytics](../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Wymagania wstępne 
Przed rozpoczęciem sprawdź następujące wymagania.

* Usługa log Analytics obsługuje tylko System Center Operations Manager 1807, Operations Manager 1801, Operations Manager 2016 Operations Manager 2012 SP1 UR6 lub nowszej, a programu Operations Manager 2012 R2 UR2 lub nowszej. Obsługa serwera proxy została dodana w programach Operations Manager 2012 SP1 UR7 i Operations Manager 2012 R2 UR3.
* Wszystkie agenty programu Operations Manager muszą spełniać minimalne wymagania dotyczące obsługi. Upewnij się, że agenci są przy minimalnej aktualizacji, w przeciwnym razie komunikacji agenta Windows może zakończyć się niepowodzeniem i generować błędy w dzienniku zdarzeń programu Operations Manager.
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, przejrzyj [Omówienie obszaru roboczego usługi Log Analytics](log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json).
* Uwierzytelnianie na platformie Azure przy użyciu konta będącego członkiem [roli Współautor usługi Log Analytics](log-analytics-manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Ostatnie zmiany do interfejsów API platformy Azure będzie uniemożliwić klientom pomyślnie skonfigurować integrację między grupą zarządzania i usługi Log Analytics po raz pierwszy. W przypadku klientów, którzy zintegrowano już program ich grupy zarządzania w usłudze nie są zagrożone o ile nie trzeba ponownie skonfigurować istniejące połączenie.  
>Nowy pakiet administracyjny został zwolniony do następujących wersji programu Operations Manager:
>  
>* Aby System Center Operations Manager 1801 Pobierz pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Dla programu System Center 2016 — Operations Manager, należy pobrać pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Dla programu System Center Operations Manager 2012 R2, należy pobrać pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Ta aktualizacja pakietu zarządzania nie ma zastosowania do System Center Operations Manager 1807, który jest wersja pakietu aktualizacji z wersji 1801 i nie pełnej kompilacji produktu.   

### <a name="network"></a>Sieć
W poniższej tabeli zawarto informacje o konfiguracji serwera proxy i zapory, które są wymagane przez agenta programu Operations Manager, serwery zarządzania i konsolę zarządzania operacjami, aby móc komunikować się z usługą Log Analytics. Ruch z każdego składnika jest ruchem wychodzącym z sieci do usługi Log Analytics.   

|Zasób | Numer portu| Obejście inspekcji HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Yes|  
|\*.blob.core.windows.net| 443|Yes|  
|\*.azure-automation.net| 443|Yes|  
|**Serwer zarządzania**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Yes|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Yes|  
|**Konsola programu Operations Manager do usługi Log Analytics**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 i 443||  
|\*.microsoft.com| 80 i 443||  
|\*.microsoftonline.com| 80 i 443||  
|\*.mms.microsoft.com| 80 i 443||  
|login.windows.net| 80 i 443||  
|portal.loganalytics.io| 80 i 443||
|api.loganalytics.io| 80 i 443||
|docs.loganalytics.io| 80 i 443||  

### <a name="tls-12-protocol"></a>Protokół TLS 1.2
Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Log Analytics, zdecydowanie zachęcamy do skonfigurowania grupy zarządzania i agent do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**. Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-log-analytics"></a>Łączenie programu Operations Manager z usługą Log Analytics
Wykonaj następujące czynności, aby skonfigurować grupę zarządzania programu Operations Manager na potrzeby nawiązywania połączenia z jednym z obszarów roboczych usługi Log Analytics.

Podczas początkowego rejestrowania grupy zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics opcję, aby określić konfigurację serwera proxy dla grupy zarządzania nie jest dostępne w konsoli operacje.  Ta opcja staje się dostępna dopiero po pomyślnym zarejestrowaniu grupy zarządzania w usłudze.  Aby obejść ten problem, należy zaktualizować system konfiguracji serwera proxy, za pomocą polecenia Netsh w systemie konsoli operacje z systemem do konfigurowania integracji i wszystkich serwerów zarządzania w grupie zarządzania.  

1. Otwórz wiersz polecenia z podwyższonymi uprawnieniami.
   a. Przejdź do **Start** i typ **cmd**.
   b. Kliknij prawym przyciskiem myszy **polecenia** i wybierz polecenie Uruchom jako administrator **.
1. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu poniższe kroki, aby zintegrować z usługą Log Analytics, możesz usunąć konfigurację, uruchamiając `netsh winhttp reset proxy` , a następnie użyj **skonfiguruj poświadczenia serwera proxy** opcję w konsoli operacje, aby określić serwer proxy lub dziennika Serwer bramy usługi Analytics. 

1. W konsoli programu Operations Manager wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Zarejestruj się w konsoli Operations Management Suite**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Uwierzytelnianie** wprowadź adres e-mail lub numer telefonu i hasło konta administratora, które jest skojarzone z subskrypcją pakietu OMS, a następnie kliknij przycisk **Zaloguj się**.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywana usługi Log Analytics.
   
1. Po pomyślnym uwierzytelnieniu na **Kreator przechodzenia do pakietu Operations Management Suite: Wybieranie obszaru roboczego** stronie zostanie wyświetlony monit wybierz Twojej dzierżawy platformy Azure, subskrypcja i obszar roboczy usługi Log Analytics. Jeśli masz więcej niż jeden obszar roboczy, wybierz z listy rozwijanej ten obszar roboczy, który ma zostać zarejestrowany w grupie zarządzania programu Operations Manager, a następnie kliknij przycisk **Dalej**.
   
   > [!NOTE]
   > Program Operations Manager obsługuje jednocześnie tylko jeden obszar roboczy usługi Log Analytics. Połączenie i komputery, które były zarejestrowane w usłudze Log Analytics w kontekście poprzedniego obszaru roboczego, zostaną usunięte z usługi Log Analytics.
   > 
   > 
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Podsumowanie** sprawdź ustawienia i jeśli są one poprawne, kliknij przycisk **Utwórz**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Kończenie** kliknij przycisk **Zamknij**.

### <a name="add-agent-managed-computers"></a>Dodawanie komputerów zarządzanych przez agenta
Po skonfigurowaniu integracji z obszarem roboczym usługi Log Analytics, tylko nawiązuje połączenie z usługą, żadne dane nie są zbierane z agentów raportujących do grupy zarządzania. Nie dzieje, aż po skonfigurowaniu, które określone komputery zarządzane z wykorzystaniem agentów zbierania danych dla usługi Log Analytics. Obiekty komputerów można wybierać indywidualnie lub przez wybranie grupy zawierającej obiekty komputerów z systemem Windows. Nie można wybrać grupy zawierającej wystąpienia innej klasy, na przykład dysków logicznych lub baz danych SQL.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Dodaj komputer/grupę** pod nagłówkiem Akcje w prawej części okienka.
1. W oknie dialogowym **Wyszukiwanie komputerów** można przeprowadzić wyszukiwanie komputerów lub grup monitorowanych przez program Operations Manager. Wybierz komputery lub grupy, które chcesz dołączyć do usługi Log Analytics, kliknij przycisk **Dodaj**, a następnie przycisk **OK**.

Komputery i grupy skonfigurowane na potrzeby zbierania danych z węzła Komputery zarządzane można wyświetlić w sekcji Operations Management Suite w obszarze roboczym **Administracja** konsoli zarządzania operacjami. W tym miejscu możesz odpowiednio do potrzeb dodawać lub usuwać komputery i grupy.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurowanie ustawień serwera proxy w konsoli zarządzania operacjami
Wykonaj następujące czynności, czy wewnętrzny serwer proxy między grupą zarządzania a usługa Log Analytics. Te ustawienia są centralnie zarządzane z poziomu grupy zarządzania i przekazywane do tych systemów zarządzanych przez agenta, które zostały wyznaczone do zbieranie danych dla usługi Log Analytics.  Jest to korzystne, gdy niektóre rozwiązania pomijają serwer zarządzania i wysyłają dane bezpośrednio do usługi.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite, a następnie kliknij pozycję **Połączenia**.
1. W widoku Połączenie OMS kliknij pozycję **Konfiguruj serwer proxy**.
1. Na stronie **Kreator konsoli Operations Management Suite: Serwer proxy** wybierz pozycję **Użyj serwera proxy, aby uzyskać dostęp do konsoli Operations Management Suite**, a następnie wpisz adres URL z numerem portu, na przykład http://corpproxy:80 i kliknij przycisk **Zakończ**.

Jeśli Twój serwer proxy wymaga uwierzytelniania, należy wykonać poniższe kroki, aby skonfigurować poświadczenia i ustawienia, które muszą zostać poddane propagacji na zarządzanych komputerach, które raporty do usługi Log Analytics w grupie zarządzania.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. W obszarze **Konfiguracja Uruchom jako** wybierz pozycję **Profile**.
1. Otwórz profil **Serwer proxy profilu Uruchom jako usługi System Center Advisor**.
1. W kreatorze profilu Uruchom jako kliknij przycisk Dodaj, aby użyć konta Uruchom jako. Możesz utworzyć [konto Uruchom jako](https://technet.microsoft.com/library/hh321655.aspx) lub użyć istniejącego konta. Konto to musi mieć wystarczające uprawnienia do komunikacji za pośrednictwem serwera proxy.
1. Aby ustawić konto do zarządzania, wybierz pozycję **Wybrana klasa, grupa lub obiekt**, kliknij polecenie **Wybierz...**, a następnie kliknij pozycję **Grupuj...** w celu otwarcie okna **Wyszukiwanie grup**.
1. Wyszukaj **grupę serwerów monitorowania usługi Microsoft System Center Advisor**, a następnie ją wybierz. Po wybraniu grupy kliknij przycisk **OK**, aby zamknąć okno **Wyszukiwanie grup**.
1. Kliknij przycisk **OK**, aby zamknąć okno **Dodawanie konta Uruchom jako**.
1. Kliknij przycisk **Zapisz**, aby zakończyć pracę kreatora i zapisać zmiany.

Po utworzeniu połączenia i skonfigurowaniu agentów, którzy będą zbierać dane i raportować je do usługi Log Analytics, następująca konfiguracja zostanie zastosowana w grupie zarządzania, niekoniecznie w przedstawionej kolejności:

* Tworzone jest konto Uruchom jako o nazwie **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Zostaje ono skojarzone z profilem Uruchom jako **Microsoft System Center Advisor Run As Profile Blob** i dotyczy dwóch klas — **Collection Server** i **Operations Manager Management Group**.
* Tworzone są dwa łączniki.  Pierwszy nosi nazwę **Microsoft.SystemCenter.Advisor.DataConnector** i jest automatycznie konfigurowany z subskrypcją, która przekazuje wszystkie alerty generowane przez wystąpienia wszystkich klas w grupie zarządzania do usługi Log Analytics. Drugi łącznik jest **łącznik usługi Advisor**, który jest odpowiedzialny za komunikacji z usługą Log Analytics i udostępniania danych.
* Agenci i grupy wybrane do zbierania danych w grupie zarządzania są dodawane do **grupy serwera monitorowania usługi Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizacje pakietu administracyjnego
Po zakończeniu konfigurowania grupa zarządzania programu Operations Manager nawiązuje połączenie z usługą Log Analytics. Serwer zarządzania synchronizuje się z usługą internetową i odbiera zaktualizowane informacje o konfiguracji w postaci pakietów administracyjnych dla włączonych rozwiązań zintegrowanych z programem Operations Manager. Operations Manager sprawdza dostępność aktualizacji tych pakietów administracyjnych i automatycznie Pobierz i importuje je, gdy są one dostępne. To zachowanie jest kontrolowane w szczególności przez dwie reguły:

* **Microsoft.SystemCenter.Advisor.MPUpdate** — aktualizuje podstawowe pakiety administracyjne usługi Log Analytics. Ta reguła jest domyślnie uruchamiana co 12 godzin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** — aktualizuje pakiety administracyjne rozwiązań, które zostały włączone w obszarze roboczym. Ta reguła jest domyślnie uruchamiana co pięć (5) minut.

Możesz przesłonić te dwie reguły, aby uniemożliwić automatyczne pobieranie, wyłączając je, lub zmodyfikować częstotliwości jak często serwer zarządzania synchronizuje się z usługą Log Analytics, aby ustalić, czy nowy pakiet administracyjny jest dostępny i powinny być pobierane. Wykonaj instrukcje [How to Override a Rule or Monitor](https://technet.microsoft.com/library/hh212869.aspx) (Jak przesłonić regułę lub monitor), aby zmodyfikować parametr **Frequency** (Częstotliwość) wartością w sekundach w celu zmiany harmonogramu synchronizacji lub parametr **Enabled** (Włączone) w celu wyłączenia reguł. Przesłonięcie dotyczy wszystkich obiektów klasy Operations Manager Management Group.

Aby kontynuować, następujące usługi istniejący proces kontroli zmian kontroli wersji pakietu zarządzania w danej grupie zarządzania w środowisku produkcyjnym, można wyłączyć reguły i je włączyć w określonych godzinach, kiedy aktualizacje są dozwolone. Jeśli w swoim środowisku masz grupę deweloperską lub grupę zarządzania kontroli jakości, która ma połączenie z Internetem, możesz skonfigurować tę grupę zarządzania za pomocą obszaru roboczego usługi Log Analytics do obsługi tego scenariusza. Dzięki temu będzie można przejrzeć i ocenić iteracyjne wydania pakietów administracyjnych usługi Log Analytics przed ich wdrożeniem w produkcyjnej grupie zarządzania.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Przełączanie grupy programu Operations Manager do nowego obszaru roboczego usługi Log Analytics
1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics** i utwórz obszar roboczy.  
1. Otwórz konsolę programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł usługi Log Analytics, a następnie wybierz pozycję **połączeń**.
1. Wybierz link **Skonfiguruj ponownie konsolę Operation Management Suite** na środku panelu.
1. Postępuj zgodnie z **Kreator przechodzenia do usługi Log Analytics** i wprowadź adres e-mail adres lub numer telefonu oraz hasło konta administratora, który jest skojarzony z nowym obszarem roboczym usługi Log Analytics.
   
   > [!NOTE]
   > Na stronie **Kreator dołączania konsoli Operations Management Suite: Wybieranie obszaru roboczego** przedstawiono istniejący używany obszar roboczy.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Sprawdzanie poprawności integracji programu Operations Manager z usługą Log Analytics
Istnieje kilka różnych sposobów weryfikowania pomyślnej integracji usługi Log Analytics z programem Operations Manager.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Potwierdzanie integracji w witrynie Azure Portal
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane.
1. Na liście obszarów roboczych usługi Log Analytics wybierz odpowiedni obszar roboczy.  
1. Wybierz kolejno pozycje **Ustawienia zaawansowane**, **Połączone źródła**, **System Center**. 
1. W tabeli w sekcji System Center Operations Manager powinna zostać wyświetlona nazwa grupy zarządzania z liczbą agentów i stanem z godziny ostatniego odebrania danych.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Potwierdzanie integracji w konsoli zarządzania operacjami
1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Wybierz pozycję **Pakiety administracyjne** i w polu tekstowym **Wyszukaj:** wpisz **Advisor** lub **Intelligence**.
1. W zależności od włączonych rozwiązań w wynikach wyszukiwania zobaczysz nazwę odpowiedniego pakietu administracyjnego.  Na przykład jeśli włączono rozwiązanie Alert Management, na liście zostanie wyświetlony pakiet administracyjny Microsoft System Center Advisor Alert Management.
1. Z widoku **Monitorowanie** przejdź do widoku **Operations Management Suite\Stan kondycji**.  W okienku **Stan serwera zarządzania** wybierz serwer zarządzania i w okienku **Widok szczegółów** potwierdź, że wartość właściwości **Identyfikator URI usługi uwierzytelniania** jest zgodny z identyfikatorem obszaru roboczego usługi Log Analytics.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Usuwanie integracji z usługą Log Analytics
Jeśli integracja między grupą zarządzania programu Operations Manager i obszarem roboczym usługi Log Analytics nie jest już potrzebna, należy wykonać kilka czynności, aby prawidłowo usunąć połączenie i konfigurację grupy zarządzania. Wykonanie poniższej procedury spowoduje zaktualizowanie obszaru roboczego usługi Log Analytics przez usunięcie odwołania do grupy zarządzania, usunięcie łączników usługi Log Analytics, a następnie usunięcie pakietów administracyjnych obsługujących integrację z usługą.  

Usunięcie z grupy zarządzania pakietów administracyjnych dla włączonych rozwiązań zintegrowanych z programem Operations Manager i pakietów administracyjnych wymaganych do obsługi integracji z usługą Log Analytics nie jest łatwe. Dzieje się tak, ponieważ niektóre z pakietów administracyjnych usługi Log Analytics są zależne od innych pokrewnych pakietów administracyjnych. Aby usunąć pakiety administracyjne, które są zależne od innych pakietów administracyjnych, pobierz skrypt [usuwający pakiet administracyjny z zależnościami](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z Centrum skryptów w witrynie TechNet.  

1. Otwórz powłokę poleceń programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
   
    > [!WARNING]
    > Przed przejściem do następnego kroku upewnij się, nie masz żadnych niestandardowych pakietów administracyjnych z ciągiem Advisor lub IntelligencePack w nazwie. W przeciwnym razie kolejne kroki spowodują ich usunięcie z grupy zarządzania.
    > 

1. W wierszu polecenia powłoki wpisz `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Następnie wpisz `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Aby usunąć wszystkie pozostałe pakiety administracyjne, które są zależne od innych pakietów administracyjnych programu System Center Advisor, użyj skryptu *RecursiveRemove.ps1* pobranego wcześniej z Centrum skryptów w witrynie TechNet.  
 
    > [!NOTE]
    > Krok, aby usunąć pakiety zarządzania klasyfikatora przy użyciu programu PowerShell nie usunie automatycznie pakietów administracyjnych programu Microsoft System Center Advisor lub programu Microsoft System Center Advisor wewnętrznego.  Nie należy próbować usunąć je.  
    >  

1. Otwórz konsolę zarządzania operacjami programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
1. W obszarze **Administracja** wybierz węzeł **Pakiety administracyjne**, a następnie w polu tekstowym **Wyszukaj:** wpisz **Advisor** i upewnij się, że następujące pakiety administracyjne są nadal zaimportowane w Twojej grupie zarządzania:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. W witrynie Azure portal kliknij pozycję **ustawienia** kafelka.
1. Wybierz **połączonych źródeł**.
1. W tabeli w sekcji System Center Operations Manager powinien zostać wyświetlony nazwę grupy zarządzania, który chcesz usunąć z obszaru roboczego. W kolumnie **Ostatnie dane** kliknij link **Usuń**.  
   
    > [!NOTE]
    > Link **Usuń** nie będzie dostępny po 14 dniach, jeśli nie zostanie wykryta żadna aktywność w połączonej grupie zarządzania.  
    > 

1. Zostanie wyświetlone okno z prośbą o potwierdzenie operacji usunięcia.  Kliknij pozycję **Tak**, aby kontynuować. 

Aby usunąć dwa łączniki — jeden o nazwie Microsoft.SystemCenter.Advisor.DataConnector, a drugi o nazwie Advisor Connector, zapisz poniższy skrypt programu PowerShell na swoim komputerze i wykonaj go, korzystając z poniższych przykładów:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Z wyjątkiem serwera zarządzania komputer, na którym uruchomisz ten skrypt, powinien mieć zainstalowaną odpowiednią powłokę poleceń programu Operations Manager w zależności od wersji grupy zarządzania.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

W przyszłości, jeśli planujesz ponowne nawiązywanie połączenia grupy zarządzania do obszaru roboczego usługi Log Analytics, należy ponownie zaimportować `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` pliku pakietu administracyjnego. W zależności od wersji programu System Center Operations Manager wdrożonego w środowisku ten plik można znaleźć w następującej lokalizacji:

* Na nośniku źródłowym w folderze `\ManagementPacks` dla programu System Center 2016 — Operations Manager lub nowszego.
* W najnowszym pakiecie zbiorczym aktualizacji zastosowanym w grupie zarządzania. Dla programu Operations Manager 2012 folderem źródłowym jest folder ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`, a dla wersji 2012 R2 jest to folder `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Kolejne kroki
Aby dodać funkcje i zebrać dane, zobacz [Add Log Analytics solutions from the Solutions Gallery](../azure-monitor/insights/solutions.md) (Dodawanie rozwiązania Log Analytics z galerii rozwiązań).


