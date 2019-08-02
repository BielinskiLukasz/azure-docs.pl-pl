---
title: Łączniki dla Azure Logic Apps
description: Automatyzowanie przepływów pracy za pomocą łączników dla Azure Logic Apps, w tym wbudowanego, zarządzanego, lokalnego konta integracji i łączników przedsiębiorstwa
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 126c3fb348b1d53769a818ae4b21fcdbbef65615
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517233"
---
# <a name="connectors-for-azure-logic-apps"></a>Łączniki dla Azure Logic Apps

Łączniki zapewniają szybki dostęp do zdarzeń, danych i akcji w ramach innych aplikacji, usług, systemów, protokołów i platform z poziomu usługi Azure Logic Apps. Korzystając z łączników w aplikacjach logiki, rozszerzasz możliwości aplikacji w chmurze i aplikacji lokalnych w celu wykonywania zadań względem tworzonych danych, którymi już dysponujesz.

Chociaż Logic Apps oferuje [setki łączników](https://docs.microsoft.com/connectors), w tym artykule opisano popularne i często używane łączniki, które są pomyślnie używane przez tysiące aplikacji i milionów wykonań na potrzeby przetwarzania danych i informacji. Aby znaleźć pełną listę łączników i informacje referencyjne poszczególnych łączników, takie jak wyzwalacze, akcje i limity, przejrzyj strony odwołań łącznika w obszarze [Przegląd łączników](https://docs.microsoft.com/connectors). Dowiedz się więcej o [wyzwalaczach i akcjach](#triggers-actions), [Logic Apps modelu cen](../logic-apps/logic-apps-pricing.md)i [Logic Apps szczegółach cennika](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Aby przeprowadzić integrację z usługą lub interfejsem API, który nie ma łącznika, możesz bezpośrednio wywołać usługę za pośrednictwem protokołu, takiego jak HTTP, lub utworzyć [Łącznik niestandardowy](#custom).

Łączniki są dostępne jako wbudowane wyzwalacze i akcje lub jako łączniki zarządzane:

<a name="built-in"></a>

* [**Wbudowane**](#built-ins): Te wbudowane wyzwalacze i akcje są "natywne", aby Azure Logic Apps i ułatwiają tworzenie aplikacji logiki, które są uruchamiane w harmonogramach niestandardowych, komunikowanie się z innymi punktami końcowymi, odbieranie i reagowanie na żądania oraz Wywoływanie usługi Azure Functions, Azure API Apps (Web Apps), własnych interfejsów API zarządzane i opublikowane przy użyciu usługi Azure API Management oraz zagnieżdżone Aplikacje logiki, które mogą odbierać żądania. Można również użyć wbudowanych akcji, które ułatwiają organizowanie i kontrolowanie przepływu pracy aplikacji logiki, a także współdziałanie z danymi.

  > [!NOTE]
  > Aplikacje logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mogą bezpośrednio uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure.
  > W przypadku korzystania z ISE wbudowane wyzwalacze i akcje, które wyświetlają **podstawową** etykietę, działają w tym samym ISE, co Aplikacje logiki. Aplikacje logiki, wbudowane wyzwalacze i wbudowane akcje, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu.
  >
  > Aby uzyskać więcej informacji na temat tworzenia ISEs, zobacz [nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > Aby uzyskać więcej informacji na temat cen, zobacz [Logic Apps model cen](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Zarządzane łączniki**: Te łączniki są wdrażane i zarządzane przez firmę Microsoft, które zapewniają wyzwalacze i akcje umożliwiające dostęp do usług w chmurze, systemów lokalnych lub obu, w tym pakietów Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint i innych. Niektóre łączniki obsługują specjalne scenariusze komunikacji B2B (Business-to-Business) i wymagają [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) połączonego z aplikacją logiki. Przed użyciem niektórych łączników może zajść konieczność utworzenia połączeń, które są zarządzane przez Azure Logic Apps. 

  Jeśli na przykład używasz programu Microsoft BizTalk Server, Aplikacje logiki mogą łączyć się z BizTalk Server i komunikować się z nimi za pomocą łącznika [on-premises connector BizTalk Server](#on-premises-connectors). 
  Następnie można zwiększyć lub wykonać operacje podobne do programu BizTalk w aplikacjach logiki przy użyciu łączników [konta integracji](#integration-account-connectors).

  Łączniki są klasyfikowane jako standardowe lub Enterprise. 
  [Łączniki przedsiębiorstwa](#enterprise-connectors) zapewniają dostęp do systemów przedsiębiorstwa, takich jak SAP, IBM MQ i IBM 3270, w celu uzyskania dodatkowych kosztów. Aby określić, czy łącznik jest standardem, czy Enterprise, zobacz szczegóły techniczne na stronie odniesienia poszczególnych łączników w obszarze [Przegląd łączników](https://docs.microsoft.com/connectors). 

  Możesz również identyfikować łączniki przy użyciu tych kategorii, chociaż niektóre łączniki mogą przecinać wiele kategorii. 
  Na przykład SAP to łącznik przedsiębiorstwa i łącznik On-Premises Connector:

  |   |   |
  |---|---|
  | [**Zarządzane łączniki interfejsu API**](#managed-api-connectors) | Twórz aplikacje logiki wykorzystujące usługi, takie jak Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online i wiele innych. |
  | [**Łączniki lokalne**](#on-premises-connectors) | Po zainstalowaniu i skonfigurowaniu [bramy danych lokalnych][gateway-doc]te łączniki ułatwiają aplikacjom lokalnym dostęp do systemów lokalnych, takich jak SQL Server, SharePoint Server, Oracle DB, udziały plików i inne. |
  | [**Łączniki konta integracji**](#integration-account-connectors) | Te łączniki są dostępne podczas tworzenia i regulowania konta integracji, przekształcają i sprawdzają poprawność kodu XML, kodowania i dekodowania plików prostych oraz przetwarzania komunikatów typu B2B (Business-to-Business) za pomocą protokołów AS2, EDIFACT i X12. |
  |||

  > [!NOTE]
  > Aplikacje logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mogą bezpośrednio uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure. W przypadku używania łączników ISE, standard i Enterprise, które wyświetlają etykietę **ISE** uruchamianą w tym samym ISE, co Aplikacje logiki. Łączniki, które nie wyświetlają etykiety ISE w globalnej usłudze Logic Apps.
  >
  > W przypadku systemów lokalnych, które są połączone z siecią wirtualną platformy Azure, wsuń ISE do tej sieci, aby aplikacje logiki mogły bezpośrednio uzyskiwać dostęp do tych systemów przy użyciu łącznika, który ma etykietę **ISE** , akcję http lub [Łącznik niestandardowy](#custom). Aplikacje logiki i łączniki, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu. 
  >
  > Aby uzyskać więcej informacji na temat tworzenia ISEs, zobacz [nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > Aby uzyskać więcej informacji na temat cen, zobacz [Logic Apps model cen](../logic-apps/logic-apps-pricing.md).

  Aby uzyskać pełną listę łączników i informacje referencyjne poszczególnych łączników, takie jak akcje i wyzwalacze, które są definiowane przez opis OpenAPI (dawniej Swagger) i limity, można znaleźć pełną listę w obszarze [Przegląd łączników](/connectors/). Aby uzyskać informacje o cenach, zobacz [Logic Apps model cen](../logic-apps/logic-apps-pricing.md)i [Logic Apps szczegóły cennika](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Wbudowane

Logic Apps udostępnia wbudowane wyzwalacze i akcje, dzięki czemu możesz tworzyć przepływy pracy oparte na harmonogramie, pomóc aplikacjom logiki komunikować się z innymi aplikacjami i usługami, kontrolować przepływ pracy za pośrednictwem aplikacji logiki oraz zarządzać danymi lub manipulować nimi.

|   |   |   |   | 
|---|---|---|---| 
| [![**Harmonogram** ikon][schedule-icon]<br/>interfejsu API][recurrence-doc] | — Uruchom aplikację logiki zgodnie z określonym harmonogramem, od podstawowego do złożonych cykli, z wyzwalaczem **cyklu** . <p>— Wstrzymaj aplikację logiki przez określony czas z akcją **opóźnienia** . <p>— Wstrzymywanie aplikacji logiki do określonej daty i godziny z **opóźnieniem do** wykonania akcji. | [![Ikona][http-icon]<br/>interfejsu API**http**][http-doc] | Komunikacja z dowolnym punktem końcowym za pośrednictwem protokołu HTTP z wyzwalaczami i akcjami dla protokołów HTTP, HTTP + Swagger i HTTP + webhook. | 
| [![**Żądanie** ikony][http-request-icon]<br/>interfejsu API][http-request-doc] | — Umożliwiaj wywoływanie aplikacji logiki z innych aplikacji lub usług, wyzwalanie na Event Grid zdarzeń zasobów lub wyzwalanie odpowiedzi w celu Azure Security Center alertów z wyzwalaczem **żądania** . <p>— Wysyłanie odpowiedzi do aplikacji lub usługi z akcją **odpowiedzi** . | [![**Partia** ikon][batch-icon]<br/>interfejsu API][batch-doc] | -Przetwarzaj komunikaty w partiach za pomocą wyzwalacza **wiadomości wsadowych** . <p>-Wywołaj Aplikacje logiki, które mają istniejące wyzwalacze wsadowe z akcją **Wyślij komunikaty do partii** . | 
| [![Ikona][azure-functions-icon]<br/>interfejsu API**Azure Functions**][azure-functions-doc] | Wywołaj usługi Azure Functions, które uruchamiająC# niestandardowe fragmenty kodu (lub Node. js) z aplikacji logiki. | [![Ikona][azure-api-management-icon]</br>interfejsu API**Azure API Management**][azure-api-management-doc] | Wyzwalacze wywołań i akcje zdefiniowane przez własne interfejsy API, którymi zarządzasz i publikujesz za pomocą usługi Azure API Management. | 
| [![Ikona][azure-app-services-icon]<br/>interfejsu API**Azure App Services**][azure-app-services-doc] | Wywołaj platformę Azure API Apps lub Web Apps hostowaną w Azure App Service. Wyzwalacze i akcje zdefiniowane przez te aplikacje są wyświetlane jak wszystkie inne wyzwalacze i akcje pierwszej klasy, gdy jest uwzględniana struktura Swagger. | [![Ikona][azure-logic-apps-icon]<br/>interfejsu API**Azure<br/>Logic Apps**][nested-logic-app-doc] | Wywołaj inne aplikacje logiki, które zaczynają się od wyzwalacza żądania. | 
||||| 

### <a name="control-workflow"></a>Przepływ pracy sterowania

Logic Apps udostępnia wbudowane akcje do tworzenia struktury i kontrolowania akcji w przepływie pracy aplikacji logiki:

|   |   |   |   | 
|---|---|---|---| 
| [![Wbudowany][condition-icon]<br/>**warunek** ikony][condition-doc] | Oceń warunek i uruchamiaj różne akcje w zależności od tego, czy warunek ma wartość Prawda czy fałsz. | [![Ikona][for-each-icon]</br>wbudowana**dla każdego**][for-each-doc] | Wykonaj te same czynności dla każdego elementu w tablicy. | 
| [![Wbudowany][scope-icon]<br/>**zakres** ikon][scope-doc] | Grupuj akcje w *zakresy*, które uzyskują swój stan po zakończeniu akcji w zakresie. | [![Wbudowany][switch-icon]</br>**przełącznik** ikon][switch-doc] | Grupuj działania w *przypadkach*, do których przypisano unikatowe wartości z wyjątkiem domyślnego przypadku. Uruchom tylko te przypadki, których przypisana wartość dopasowuje wynik z wyrażenia, obiektu lub tokenu. Jeśli nie ma żadnych dopasowań, uruchom przypadek domyślny. | 
| [![**Zakończenie** wbudowanej ikony][terminate-icon]<br/>][terminate-doc] | Zatrzymaj aktywnie uruchomiony przepływ pracy aplikacji logiki. | [![Ikona][until-icon]<br/>wbudowana**do**][until-doc] | Powtarzaj akcje do momentu, gdy określony warunek ma wartość true lub jakiś stan uległ zmianie. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Zarządzanie danymi i ich manipulowanie

Logic Apps udostępnia wbudowane akcje do pracy z danymi wyjściowymi i ich formatami:  

|   |   | 
|---|---| 
| [![Wbudowane][data-operations-icon]<br/>operacje na**danych** ikon][data-operations-doc] | Wykonaj operacje z danymi: <p>- **Redaguj**: Utwórz jedno wyjście z wielu danych wejściowych z różnymi typami. <br>- **Utwórz tabelę CSV**: Utwórz tabelę wartości rozdzielanych przecinkami (CSV) z tablicy z obiektami JSON. <br>- **Utwórz tabelę HTML**: Utwórz tabelę HTML z tablicy z obiektami JSON. <br>- **Filtruj tablicę**: Utwórz tablicę z elementów w innej tablicy, która spełnia kryteria. <br>- **Dołącz**: Utwórz ciąg ze wszystkich elementów w tablicy i oddziel te elementy z określonym ogranicznikiem. <br>- **Analiza JSON**: Utwórz przyjazne dla użytkownika tokeny na podstawie właściwości i ich wartości w zawartości JSON, aby można było używać tych właściwości w przepływie pracy. <br>- **Wybierz**: Utwórz tablicę z obiektami JSON, przekształcając elementy lub wartości w innej tablicy i mapując te elementy do określonych właściwości. | 
| ![Ikona wbudowana][date-time-icon]<br/>**Data i godzina** | Wykonaj operacje z sygnaturami czasowymi: <p>- **Dodaj do czasu**: Dodaj określoną liczbę jednostek do sygnatury czasowej. <br>- **Konwertuj strefę czasową**: Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. <br>- **Bieżący czas**: Zwróć bieżącą sygnaturę czasową jako ciąg. <br>- **Rozpocznij w przyszłości**: Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu. <br>- **Czas ostatniej ostatniej**: Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu. <br>- **Odejmij od czasu**: Odejmij wiele jednostek czasu od sygnatury czasowej. |
| [![Wbudowane][variables-icon]<br/>**zmienne** ikon][variables-doc] | Wykonywanie operacji przy użyciu zmiennych: <p>- **Dołącz do zmiennej tablicowej**: Wstaw wartość jako ostatni element w tablicy przechowywanej przez zmienną. <br>- **Dołącz do zmiennej ciągu**: Wstaw wartość jako ostatni znak w ciągu przechowywanym przez zmienną. <br>- **Zmniejsz zmienną**: Zmniejsz zmienną przez wartość stałą. <br>- **Zmienna przyrostu**: Zwiększ zmienną przez wartość stałą. <br>- **Zainicjuj zmienną**: Utwórz zmienną i Zadeklaruj jej typ danych i wartość początkową. <br>- **Ustaw zmienną**: Przypisz inną wartość do istniejącej zmiennej. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Zarządzane łączniki interfejsu API

Logic Apps udostępnia te popularne standardowe łączniki do automatyzowania zadań, procesów i przepływów pracy przy użyciu tych usług lub systemów.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona][azure-service-bus-icon]<br/>interfejsu API**Azure Service Bus**][azure-service-bus-doc] | Zarządzaj komunikatami asynchronicznymi, sesjami i subskrypcjami tematów przy użyciu najczęściej używanego łącznika w usłudze Logic Apps. | [![Ikona][sql-server-icon]<br/>interfejsu API**SQL Server**][sql-server-doc] | Połącz się ze swoją SQL Server lokalną lub Azure SQL Database w chmurze, aby móc zarządzać rekordami, uruchamiać procedury składowane lub wykonywać zapytania. | 
| [![Ikona][office-365-outlook-icon]<br/>interfejsu API**Office<br/>365 Outlook**][office-365-outlook-doc] | Połącz się z kontem e-mail pakietu Office 365, aby móc tworzyć wiadomości e-mail, zadania, zdarzenia kalendarza i spotkania, kontakty, żądania itp. oraz zarządzać nimi. | [![Ikona][azure-blob-storage-icon]<br/>interfejsu API**Azure<br/>BLOB Storage**][azure-blob-storage-doc] | Połącz się z kontem magazynu, aby można było tworzyć zawartość obiektów blob i zarządzać nią. | 
| [![Ikona][sftp-icon]<br/>interfejsu API**SFTP**][sftp-doc] | Łącz się z serwerami SFTP dostępnymi z Internetu, aby pracować ze swoimi plikami i folderami. | [![Ikona][sharepoint-online-icon]<br/>interfejsu API**SharePoint<br/>online**][sharepoint-online-doc] | Połącz się z usługą SharePoint Online, aby móc zarządzać plikami, załącznikami, folderami i innymi. | 
| [![Ikona][dynamics-365-icon]<br/>interfejsu API**Dynamics<br/>365 CRM Online**][dynamics-365-doc] | Połącz się z kontem Dynamics 365, aby można było tworzyć i zarządzać rekordami, elementami itd. | [![Ikona][ftp-icon]<br/>interfejsu API**FTP**][ftp-doc] | Łączenie z serwerami FTP, do których można uzyskać dostęp za pośrednictwem Internetu, aby można było korzystać z plików i folderów. | 
| [![Ikona][salesforce-icon]<br/>interfejsu API**Salesforce**][salesforce-doc] | Połącz się z kontem usługi Salesforce, aby można było tworzyć i zarządzać elementami, takimi jak rekordy, zadania, obiekty i wiele innych. | [![Ikona][twitter-icon]<br/>interfejsu API**Twitter**][twitter-doc] | Połącz się z kontem usługi Twitter, aby móc zarządzać tweetami, obserwatorami, osią czasu i wieloma innymi. Zapisz tweety w programie SQL, programie Excel lub programie SharePoint. | 
| [![Ikona][azure-event-hubs-icon]<br/>interfejsu API**Azure Event Hubs**][azure-event-hubs-doc] | Używanie i publikowanie zdarzeń za poorednictwem centrum zdarzeń. Na przykład Pobierz dane wyjściowe z aplikacji logiki za pomocą Event Hubs, a następnie wyślij je do dostawcy analiz w czasie rzeczywistym. | [![Ikona][azure-event-grid-icon]<br/>interfejsu API**Azure Event**</br>**Grid**][azure-event-grid-doc] | Monitoruj zdarzenia opublikowane przez Event Grid, na przykład w przypadku zmiany zasobów platformy Azure lub zasobów innych firm. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Łączniki lokalne 

Poniżej przedstawiono kilka typowych łączników standardowych, które Logic Apps zapewniają dostęp do danych i zasobów w systemach lokalnych. Aby można było utworzyć połączenie z systemem lokalnym, należy najpierw [pobrać, zainstalować i skonfigurować lokalną bramę danych][gateway-doc]. Ta brama zapewnia bezpieczny kanał komunikacyjny bez konieczności konfigurowania niezbędnej infrastruktury sieciowej. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Ikona interfejsu API][biztalk-server-icon]<br/>**BizTalk**</br> **Serwer** | [![][file-system-icon]<br/>**Systemplików</br>**  ikon interfejsu API][file-system-doc] | [![Ikona][ibm-db2-icon]<br/>interfejsu API**IBM DB2**][ibm-db2-doc] | [![Ikona][ibm-informix-icon]<br/>interfejsu API**IBM** </br> **Informix**][ibm-informix-doc] | ![Ikona interfejsu API][mysql-icon]<br/>**MySQL** | 
| [![Ikona][oracle-db-icon]<br/>interfejsu API**Oracle DB**][oracle-db-doc] | ![Ikona interfejsu API][postgre-sql-icon]<br/>**PostgreSQL** | [![Ikona][sharepoint-server-icon]<br/>interfejsu API **</br> programu SharePoint Server**][sharepoint-server-doc] | [![Ikona][sql-server-icon]<br/>interfejsu API**SQL</br> Server**][sql-server-doc] | ![Ikona interfejsu API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Łączniki konta integracji

Logic Apps udostępnia standardowe łączniki do tworzenia rozwiązań między firmami (B2B, Business-to-Business) za pomocą aplikacji logiki, gdy tworzysz i płacisz za [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), które jest dostępne za pomocą pakiet INTEGRACYJNY dla przedsiębiorstw (EIP) na platformie Azure. Za pomocą tego konta możesz tworzyć i przechowywać artefakty B2B, takie jak partnerzy handlowi, umowy, mapy, schematy, certyfikaty itd. Aby użyć tych artefaktów, skojarz Aplikacje logiki z kontem integracji. Jeśli obecnie używasz BizTalk Server, te łączniki mogą wydawać się już znane.

|   |   |   |   | 
|---|---|---|---| 
| [![Ikona][as2-icon]<br/>interfejsu API**dekodowanie AS2</br>** ][as2-doc] | [![Ikona][as2-icon]<br/>interfejsu API**kodowanie AS2</br>** ][as2-doc] | [![Ikona][edifact-icon]<br/>interfejsu API**dekodowanie EDIFACT</br>** ][edifact-decode-doc] | [![Ikona][edifact-icon]<br/>interfejsu API**kodowanie EDIFACT</br>** ][edifact-encode-doc] | 
| [![Ikona][flat-file-decode-icon]<br/>interfejsu API —**dekodowanie pliku</br> prostego**][flat-file-decode-doc] | [![Ikona][flat-file-encode-icon]<br/>interfejsu API — **</br> kodowanie pliku prostego**][flat-file-encode-doc] | [![Ikona][integration-account-icon]<br/>interfejsu API**konto integracji<br/>** ][integration-account-doc] | [![Ikona][liquid-icon]<br/>interfejsu API transformacje**płynne**</br>][json-liquid-transform-doc] | 
| [![Ikona][x12-icon]<br/>interfejsu API**dekodowanie X12</br>** ][x12-decode-doc] | [![Ikona][x12-icon]<br/>interfejsu API**kodowanie X12</br>** ][x12-encode-doc] | [![Ikona][xml-transform-icon]<br/>interfejsu API**przekształcenia** **XML**</br>][xml-transform-doc] | [![][xml-validate-icon]<br/>**Weryfikacja kodu XML<br/>** ikony interfejsu API][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Łączniki dla przedsiębiorstw

Logic Apps udostępnia te łączniki przedsiębiorstwa do uzyskiwania dostępu do systemów przedsiębiorstwa, takich jak SAP i IBM MQ:

|   |   |   | 
|---|---|---| 
| [![Ikona][ibm-3270-icon]<br/>interfejsu API**IBM 3270**][ibm-3270-doc] | [![Ikona][ibm-mq-icon]<br/>interfejsu API**IBM MQ**][ibm-mq-doc] | [![Ikona][sap-icon]<br/>interfejsu API**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Wyzwalacze i akcje — więcej informacji

Łączniki mogązapewniać wyzwalacze, *Akcje*lub oba te elementy. *Wyzwalacz* to pierwszy krok w dowolnej aplikacji logiki, zwykle określający zdarzenie, które wyzwala wyzwalacz i uruchamia aplikację logiki. Na przykład łącznik FTP ma wyzwalacz, który uruchamia aplikację logiki "po dodaniu lub zmodyfikowaniu pliku". Niektóre wyzwalacze regularnie sprawdzają obecność określonego zdarzenia lub danych, a następnie uruchamiają się po wykryciu określonego zdarzenia lub danych. Inne wyzwalacze oczekują, ale natychmiast po wystąpieniu określonego zdarzenia lub gdy nowe dane są dostępne. Wyzwalacze przekazują również wszystkie wymagane dane do aplikacji logiki. Aplikacja logiki może odczytywać i używać tych danych w ramach przepływu pracy.
Na przykład łącznik usługi Twitter ma wyzwalacz "po opublikowaniu nowego tweetu", który przekazuje zawartość tweetu do przepływu pracy aplikacji logiki. 

Po uruchomieniu wyzwalacza Azure Logic Apps tworzy wystąpienie aplikacji logiki i zacznie uruchamiać *Akcje* w przepływie pracy aplikacji logiki. Akcje to kroki, które obserwują wyzwalacz i wykonują zadania w przepływie pracy aplikacji logiki. Można na przykład utworzyć aplikację logiki, która pobiera dane klienta z bazy danych SQL i przetwarzać te dane w późniejszych akcjach. 

Poniżej przedstawiono ogólne rodzaje wyzwalaczy, które Azure Logic Apps zapewnia:

* *Wyzwalacz cyklu*: Ten wyzwalacz jest uruchamiany zgodnie z określonym harmonogramem i nie jest ściśle skojarzony z określoną usługą lub systemem.

* *Wyzwalacz sondowania*: Ten wyzwalacz regularnie sonduje określoną usługę lub system zgodnie z określonym harmonogramem, wyszukując nowe dane lub w przypadku wystąpienia konkretnego zdarzenia. Jeśli nowe dane są dostępne lub wystąpi określone zdarzenie, wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, która może teraz korzystać z danych, które są przesyłane jako dane wejściowe.

* *Wyzwalacz wypychania*: Ten wyzwalacz czeka i nasłuchuje w poszukiwaniu nowych danych lub zdarzenia. Jeśli nowe dane są dostępne lub gdy wystąpi zdarzenie, wyzwalacz tworzy i uruchamia nowe wystąpienie aplikacji logiki, która może teraz korzystać z danych, które są przesyłane jako dane wejściowe.

<a name="custom"></a>

## <a name="connector-configuration"></a>Konfiguracja łącznika

Wyzwalacze i akcje każdego łącznika zapewniają własne właściwości, które można skonfigurować. Wiele łączników wymaga również, aby najpierw utworzyć *połączenie* z usługą lub systemem docelowym oraz podać poświadczenia uwierzytelniania lub inne szczegóły konfiguracji, zanim będzie można użyć wyzwalacza lub akcji w aplikacji logiki. Na przykład trzeba autoryzować połączenie z kontem usługi Twitter w celu uzyskania dostępu do danych lub opublikowania w Twoim imieniu. 

W przypadku łączników korzystających z protokołu OAuth Tworzenie połączenia oznacza zalogowanie się do usługi, takiej jak Office 365, Salesforce lub GitHub, gdzie token dostępu jest szyfrowany i bezpiecznie przechowywany w magazynie kluczy tajnych platformy Azure. Inne łączniki, takie jak FTP i SQL, wymagają połączenia, które ma szczegóły konfiguracji, takie jak adres serwera, nazwa użytkownika i hasło. Te szczegóły konfiguracji połączenia są również szyfrowane i bezpiecznie przechowywane. 

Połączenia mogą uzyskać dostęp do docelowej usługi lub systemu przez cały czas, gdy zezwala na to usługa lub system. W przypadku usług korzystających z połączeń OAuth Azure Active Directory (AD), takich jak pakiet Office 365 i usługa Dynamics, Azure Logic Apps odświeża tokeny dostępu przez nieograniczony czas. Inne usługi mogą mieć limity, jak długo Azure Logic Apps mogą używać tokenu bez odświeżania. Ogólnie rzecz biorąc, niektóre akcje unieważnią wszystkie tokeny dostępu, takie jak zmiana hasła.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Niestandardowe interfejsy API i łączniki

Aby wywołać interfejsy API, które uruchamiają kod niestandardowy lub nie są dostępne jako łączniki, można rozciągnąć Logic Apps platformę przez [utworzenie API Apps niestandardowych](../logic-apps/logic-apps-create-api-app.md). Możesz również [utworzyć łączniki niestandardowe](../logic-apps/custom-connector-overview.md) dla *dowolnego* interfejsu API opartego na protokole REST lub SOAP, co sprawia, że te interfejsy API są dostępne dla dowolnej aplikacji logiki w ramach subskrypcji platformy Azure.
Aby udostępnić niestandardowe API Apps lub łączniki dla wszystkich użytkowników na platformie Azure, możesz [przesłać łączniki do certyfikacji firmy Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Aplikacje logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) mogą bezpośrednio uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure.
> Jeśli masz łączniki niestandardowe wymagające lokalnej bramy danych i utworzono te łączniki poza ISE, Aplikacje logiki w ISE mogą również używać tych łączników.
>
> Łączniki niestandardowe utworzone w ramach ISE nie współpracują z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskiwać dostęp do lokalnych źródeł danych, które są połączone z siecią wirtualną platformy Azure hostującym ISE. W związku z tym aplikacje logiki w ISE najprawdopodobniej nie potrzebują bramy danych podczas komunikowania się z tymi zasobami.
>
> Aby uzyskać więcej informacji na temat tworzenia ISEs, zobacz [nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Kolejne kroki

* Znajdź [pełną listę łączników](https://docs.microsoft.com/connectors)
* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie łączników niestandardowych dla usługi Logic Apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Tworzenie niestandardowych interfejsów API dla aplikacji logiki](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Łączenie z lokalnymi źródłami danych z aplikacji logiki za pomocą bramy danych lokalnych"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integracja aplikacji logiki z usługą Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Tworzenie wystąpienia usługi Azure API Management na potrzeby zarządzania i publikowania interfejsów API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integracja aplikacji logiki z usługą App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Wysyłanie komunikatów z tematów i kolejek usługi Service Bus oraz odbieranie komunikatów z subskrypcji i kolejek usługi Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Przetwarzaj komunikaty w grupach lub jako partie"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Oceń warunek i uruchamiaj różne akcje w zależności od tego, czy warunek ma wartość Prawda czy fałsz"
[delay-doc]: ./connectors-native-delay.md "Wykonywanie akcji z opóźnieniem"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Wykonaj te same czynności dla każdego elementu w tablicy"
[http-doc]: ./connectors-native-http.md "Nawiązywanie połączeń HTTP za pomocą łącznika HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-response-doc]: ./connectors-native-reqres.md "Dodawanie akcji dla żądań i odpowiedzi HTTP w aplikacjach logiki"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Nawiązywanie połączeń HTTP za pomocą łącznika protokołu HTTP + Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Dodawanie akcji elementu webhook protokołu HTTP i wyzwalaczy do aplikacji logiki"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integracja aplikacji logiki z zagnieżdżonymi przepływami pracy"
[query-doc]: ./connectors-native-query.md "Wybieranie i filtrowanie tablic za pomocą akcji zapytania"
[recurrence-doc]:  ./connectors-native-recurrence.md "Wyzwalanie akcji cyklicznych dla aplikacji logiki"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizuj akcje w grupach, które uzyskują swój stan po zakończeniu działania w grupie." 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizuj działania w przypadkach, które są przypisanymi unikatowymi wartościami. Uruchom tylko przypadek, którego wartość pasuje do wyniku z wyrażenia, obiektu lub tokenu. Jeśli nie istnieją żadne dopasowania, uruchom przypadek domyślny"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zatrzymywanie lub anulowanie aktywnie działającego przepływu pracy dla aplikacji logiki"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Powtarzaj akcje do momentu, gdy określony warunek ma wartość true lub zmieniono jakiś stan"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Wykonywanie operacji na danych, takich jak filtrowanie tablic lub Tworzenie woluminów CSV i tabel HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Wykonywanie operacji ze zmiennymi, takimi jak Initialize, Set, Increment, Zmniejsz i Dołącz do zmiennej String lub Array"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Zarządzanie plikami w kontenerze obiektów blob za pomocą łącznika usługi Azure Blob Storage"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitoruj zdarzenia opublikowane przez Event Grid, na przykład w przypadku zmiany zasobów platformy Azure lub zasobów innych firm"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Łączenie z usługą Azure Event Hubs. Odbieranie i wysyłanie zdarzeń między aplikacjami logiki i usługą Event Hubs"
[box-doc]: ./connectors-create-api-box.md "Łączenie z usługą Box. Przekazywanie, pobieranie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Łączenie z usługą Dropbox. Przekazywanie, pobieranie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Łączenie z usługą Dynamics CRM Online w celu korzystania z danych usługi CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Łączenie z serwisem Facebook. Publikowanie na osi czasu, uzyskiwanie kanału informacyjnego strony i inne funkcje"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Łączenie z lokalnym systemem plików"
[ftp-doc]: ./connectors-create-api-ftp.md "Łączenie z serwerem FTP/FTPS i wykonywanie zadań związanych z protokołem FTP, takich jak np. przekazywanie, pobieranie i usuwanie plików"
[github-doc]: ./connectors-create-api-github.md "Łączenie z witryną GitHub i śledzenie problemów"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Nawiązuje połączenie z usługą Kalendarz Google i może zarządzać kalendarzem."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Łączenie z Dyskiem Google i korzystanie z danych"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Połącz się z arkuszami Google, aby móc modyfikować arkusze"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Łączenie z Zadaniami Google i zarządzanie zadaniami"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Nawiązywanie połączenia z aplikacjami 3270 na komputerze mainframe firmy IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Łączenie z bazą danych IBM DB2 w chmurze lub lokalnie. Aktualizowanie wiersza, pobieranie tabeli oraz inne funkcje"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Łączenie z usługą Informix w chmurze lub lokalnie. Odczytywanie wiersza, wyświetlanie listy tabel i inne funkcje"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Nawiązywanie połączenia z programem IBM MQ lokalnie lub na platformie Azure w celu wysyłania i odbierania wiadomości"
[instagram-doc]: ./connectors-create-api-instagram.md "Łączenie z usługą Instagram. Wyzwalanie zdarzeń lub reagowanie na nie"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Łączenie z kontem MailChimp. Automatyzowanie obsługi wiadomości e-mail i zarządzanie nimi"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Łączenie z usługą Mandrill w celu komunikowania się"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Łączenie z usługą Microsoft Translator. Tłumaczenie tekstu, wykrywanie języków i inne funkcje" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Łączenie z kontem usługi Office 365. Wysyłanie i odbieranie wiadomości e-mail, zarządzanie kalendarzem i kontaktami oraz inne funkcje"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Pobieranie informacji o filmach wideo, list i kanałów wideo oraz adresów URL odtwarzania dla filmów wideo usługi Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Łączenie z osobistą usługą Microsoft OneDrive. Przekazywanie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Łączenie z firmową usługą Microsoft OneDrive. Przekazywanie, usuwanie i wyświetlanie listy plików oraz inne funkcje"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Łączenie z bazą danych Oracle w celu m.in. dodawania, wstawiania i usuwania wierszy"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Łączenie ze skrzynką pocztową programu Outlook. Zarządzanie pocztą e-mail, kalendarzami i kontaktami oraz inne funkcje"
[project-online-doc]: ./connectors-create-api-projectonline.md "Łączenie z usługą Microsoft Project Online. Zarządzanie projektami, zadaniami i zasobami oraz inne funkcje"
[rss-doc]: ./connectors-create-api-rss.md "Publikowanie i pobieranie elementów kanału informacyjnego, wyzwalanie operacji po opublikowaniu nowego elementu w kanale informacyjnym RSS."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Łączenie z kontem usługi Salesforce. Zarządzanie kontami, potencjalnymi klientami i możliwościami sprzedaży oraz inne funkcje"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Łączenie z lokalnym systemem SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Łączenie z usługą SendGrid. Wyślij wiadomość e-mail i Zarządzaj listami adresatów"
[sftp-doc]: ./connectors-create-api-sftp.md "Łączenie z kontem SFTP. Przekazywanie, pobieranie i usuwanie plików oraz inne funkcje"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Łączenie z programem SharePoint na serwerze lokalnym. Zarządzanie dokumentami i elementami list oraz inne funkcje"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Łączenie z usługą SharePoint Online. Zarządzanie dokumentami i elementami list oraz inne funkcje"
[slack-doc]: ./connectors-create-api-slack.md "Łączenie z usługą Slack i publikowanie wiadomości w kanałach Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Nawiązywanie połączenia z serwerem SMTP i wysyłanie wiadomości e-mail z załącznikami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Łączenie z usługą SparkPost w celu komunikowania się"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Połącz się z Azure SQL Database lub SQL Server. Tworzenie, aktualizowanie, pobieranie i usuwanie wpisów w tabeli bazy danych SQL."
[trello-doc]: ./connectors-create-api-trello.md "Łączenie z usługą Trello. Zarządzanie projektami i organizowanie dowolnych rzeczy z dowolnymi osobami"
[twilio-doc]: ./connectors-create-api-twilio.md "Łączenie z usługą Twilio. Wysyłanie i pobieranie wiadomości, pobieranie dostępnych numerów, zarządzanie przychodzącymi numerami telefonów i inne funkcje"
[twitter-doc]: ./connectors-create-api-twitter.md "Łączenie z serwisem Twitter. Pobieranie osi czasu, publikowanie tweetów i inne funkcje"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Łączenie z usługą Wunderlist. Zarządzanie zadaniami i listami zadań do wykonania, synchronizowanie danych oraz inne funkcje"
[yammer-doc]: ./connectors-create-api-yammer.md "Łączenie z usługą Yammer. Publikowanie wiadomości, pobieranie nowych wiadomości i inne funkcje"
[youtube-doc]: ./connectors-create-api-youtube.md "Łączenie z serwisem YouTube. Zarządzanie filmami wideo i kanałami"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Więcej informacji na temat integracji dla przedsiębiorstw — AS2."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Więcej informacji na temat integracji dla przedsiębiorstw — dekodowanie komunikatów EDIFACT."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Więcej informacji na temat integracji dla przedsiębiorstw — kodowanie komunikatów EDIFACT."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Więcej informacji na temat integracji dla przedsiębiorstw — plik prosty."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Więcej informacji na temat integracji dla przedsiębiorstw — plik prosty."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Wyszukiwanie schematów, map partnerów i innych elementów na koncie integracji"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Informacje na temat przekształceń JSON i łącznika Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Więcej informacji na temat integracji dla przedsiębiorstw — X12."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Więcej informacji na temat integracji dla przedsiębiorstw — dekodowanie komunikatów X12."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Więcej informacji na temat integracji dla przedsiębiorstw — kodowanie komunikatów X12."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Więcej informacji na temat integracji dla przedsiębiorstw — transformacje."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Więcej informacji na temat integracji dla przedsiębiorstw — walidacja XML."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
