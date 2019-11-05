---
title: Konfigurowanie aplikacji w portalu — Azure App Service
description: Dowiedz się, jak skonfigurować typowe ustawienia aplikacji App Service w Azure Portal.
keywords: Azure App Service, aplikacja sieci Web, ustawienia aplikacji, zmienne środowiskowe
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c22f88487fd8b34d48d3012c706bb0415760b21e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470941"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Skonfiguruj aplikację App Service w Azure Portal

W tym temacie opisano sposób konfigurowania typowych ustawień aplikacji sieci Web, zaplecza mobilnego lub aplikacji interfejsu API przy użyciu [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji

W App Service ustawienia aplikacji są zmienne, które są przesyłane jako zmienne środowiskowe do kodu aplikacji. W przypadku aplikacji systemu Linux i kontenerów niestandardowych App Service przekazuje ustawienia aplikacji do kontenera przy użyciu flagi `--env` w celu ustawienia zmiennej środowiskowej w kontenerze.

W [Azure Portal]przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji kliknij pozycję **konfiguracja** > **Ustawienia aplikacji**.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku deweloperów ASP.NET i ASP.NET Core, Ustawianie ustawień aplikacji w App Service przypomina ich ustawianie w `<appSettings>` w *pliku Web. config* lub *appSettings. JSON*, ale wartości w App Service zastępują te pliki w *pliku Web. config* lub *appSettings. JSON* . Ustawienia programistyczne (na przykład lokalne hasło MySQL) można zachować w *pliku Web. config* lub *appSettings. JSON*, ale klucze tajne produkcji (na przykład hasło bazy danych Azure mysql) bezpiecznie w App Service. Ten sam kod używa ustawień programistycznych podczas debugowania lokalnego i korzysta z wpisów tajnych produkcji w przypadku wdrożenia na platformie Azure.

Inne stosy języka, podobnie, pobierają ustawienia aplikacji jako zmienne środowiskowe w czasie wykonywania. Aby zapoznać się z procedurami specyficznymi dla stosu, zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](containers/configure-custom-container.md#configure-environment-variables)

Ustawienia aplikacji są zawsze szyfrowane, gdy są przechowywane (szyfrowane w trybie REST).

> [!NOTE]
> Ustawienia aplikacji mogą być również rozwiązywane z [Key Vault](/azure/key-vault/) przy użyciu [Key Vault odwołań](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości ustawień aplikacji są ukrywane w portalu w celu zabezpieczenia. Aby wyświetlić ukrytą wartość ustawienia aplikacji, kliknij pole **wartość** tego ustawienia. Aby wyświetlić wartości wszystkich ustawień aplikacji, kliknij przycisk **Pokaż wartość** .

### <a name="add-or-edit"></a>Dodaj lub edytuj

Aby dodać nowe ustawienie aplikacji, kliknij pozycję **nowe ustawienie aplikacji**. W oknie dialogowym można [przykleić ustawienie do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienia, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

> [!NOTE]
> W domyślnym kontenerze systemu Linux lub dla niestandardowego kontenera systemu Linux każda zagnieżdżona struktura klucza JSON w nazwie ustawienia aplikacji, taka jak `ApplicationInsights:InstrumentationKey`, musi być skonfigurowana w App Service jako `ApplicationInsights__InstrumentationKey` dla nazwy klucza. Innymi słowy, każda `:` powinna zostać zastąpiona przez `__` (podwójne podkreślenie).
>

### <a name="edit-in-bulk"></a>Edytuj luzem

Aby dodać lub edytować ustawienia aplikacji w trybie zbiorczym, kliknij przycisk **Edytuj zaawansowane** . Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

Ustawienia aplikacji mają następujące formatowanie JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Konfigurowanie parametrów połączenia

W [Azure Portal]przejdź do strony zarządzania aplikacją. W menu po lewej stronie aplikacji kliknij pozycję **konfiguracja** > **Ustawienia aplikacji**.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku deweloperów ASP.NET i ASP.NET Core, ustawianie parametrów połączenia w App Service przypomina Ustawianie ich w `<connectionStrings>` w *Web. config*, ale wartości ustawionych w App Service przesłaniają te elementy w *pliku Web. config*. Ustawienia programistyczne (na przykład plik bazy danych) można zachować w pliku *Web. config* i w tajemnicach produkcyjnych (na przykład SQL Database poświadczenia) bezpiecznie w App Service. Ten sam kod używa ustawień programistycznych podczas debugowania lokalnego i korzysta z wpisów tajnych produkcji w przypadku wdrożenia na platformie Azure.

W przypadku innych stosów języka lepiej jest używać [ustawień aplikacji](#configure-app-settings) , ponieważ parametry połączeń wymagają specjalnego formatowania w kluczach zmiennych w celu uzyskania dostępu do wartości. Poniżej przedstawiono jeden wyjątek: w przypadku konfigurowania parametrów połączenia w aplikacji kopie zapasowe niektórych typów baz danych platformy Azure są tworzone razem z aplikacją. Aby uzyskać więcej informacji, zobacz [co to jest kopia zapasowa](manage-backup.md#what-gets-backed-up). Jeśli ta zautomatyzowana kopia zapasowa nie jest potrzebna, Użyj ustawień aplikacji.

W czasie wykonywania parametry połączenia są dostępne jako zmienne środowiskowe poprzedzone prefiksem następujących typów połączeń:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Niestandardowe: `CUSTOMCONNSTR_`

Na przykład parametry połączenia MySql o nazwie *connectionstring1* są dostępne jako zmienna środowiskowa `MYSQLCONNSTR_connectionString1`. Aby zapoznać się z procedurami specyficznymi dla stosu, zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](containers/configure-custom-container.md#configure-environment-variables)

Parametry połączenia są zawsze szyfrowane, gdy są przechowywane (szyfrowane w trybie REST).

> [!NOTE]
> Parametry połączenia można również rozpoznać z [Key Vault](/azure/key-vault/) przy użyciu [Key Vault odwołań](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości parametrów połączenia są ukryte w portalu w celu zabezpieczenia. Aby wyświetlić ukrytą wartość parametrów połączenia, po prostu kliknij pole **wartość** tego ciągu. Aby wyświetlić wartości wszystkich parametrów połączenia, kliknij przycisk **Pokaż wartość** .

### <a name="add-or-edit"></a>Dodaj lub edytuj

Aby dodać nowe parametry połączenia, kliknij przycisk **nowe parametry połączenia**. W oknie dialogowym można [przykleić parametry połączenia do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienia, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

### <a name="edit-in-bulk"></a>Edytuj luzem

Aby dodać lub edytować parametry połączenia zbiorczo, kliknij przycisk **Edytuj zaawansowane** . Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

Parametry połączenia mają następujące formatowanie JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurowanie ustawień ogólnych

W [Azure Portal]przejdź do strony zarządzania aplikacją. W menu po lewej stronie aplikacji kliknij pozycję **konfiguracja** > **Ustawienia aplikacji**.

![Ustawienia ogólne](./media/configure-common/open-general.png)

Tutaj można skonfigurować niektóre typowe ustawienia dla aplikacji. Niektóre ustawienia wymagają [skalowania do wyższych warstw cenowych](manage-scale-up.md).

- **Ustawienia stosu**: stos oprogramowania do uruchamiania aplikacji, w tym wersje język i zestaw SDK. W przypadku aplikacji systemu Linux i niestandardowych aplikacji kontenerów można również ustawić opcjonalne lub plik startowy.
- **Ustawienia platformy**: umożliwia skonfigurowanie ustawień platformy hostingu, w tym:
    - Liczba **bitów**: 32-bitowe lub 64-bitowe.
    - **Protokół WebSocket**: na przykład dla [ASP.NET sygnalizujący] lub [Socket.IO](https://socket.io/).
    - **Zawsze włączone**: Zachowaj załadowanie aplikacji nawet wtedy, gdy nie ma ruchu. Jest to wymagane w przypadku ciągłych zadań WebJob lub zadań WebJob, które są wyzwalane przy użyciu wyrażenia firmy cronus.
    - **Wersja potoku zarządzanego**: [tryb potokowy]usług IIS. Ustaw ją na **klasyczny** , jeśli masz starszą aplikację, która wymaga starszej wersji usług IIS.
    - **Wersja protokołu HTTP**: ustaw na **2,0** , aby włączyć obsługę protokołu [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > Większość nowoczesnych przeglądarek obsługuje protokół HTTP/2 tylko w przypadku protokołu TLS, podczas gdy ruch nieszyfrowany nadal używa protokołu HTTP/1.1. Aby zapewnić, że przeglądarki klienta nawiązują połączenie z aplikacją przy użyciu protokołu HTTP/2, [Zabezpiecz swoją niestandardową nazwę DNS z powiązaniem SSL w Azure App Service](configure-ssl-bindings.md).
    - **Koligacja ARR**: w przypadku wdrożenia obejmującego wiele wystąpień upewnij się, że klient jest kierowany do tego samego wystąpienia w okresie istnienia sesji. Tę opcję można ustawić na wartość **off** dla bezstanowych aplikacji.
- **Debugowanie**: Włącz debugowanie zdalne dla aplikacji [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)lub [Node. js](containers/configure-language-nodejs.md#debug-remotely) . Ta opcja jest włączana automatycznie po 48 godzinach.
- **Przychodzące certyfikaty klienta**: Wymagaj certyfikatów klienta w ramach [uwierzytelniania wzajemnego](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurowanie dokumentów domyślnych

To ustawienie dotyczy tylko aplikacji systemu Windows.

W [Azure Portal]przejdź do strony zarządzania aplikacją. W menu po lewej stronie aplikacji kliknij pozycję **konfiguracja** > **dokumenty domyślne**.

![Ustawienia ogólne](./media/configure-common/open-documents.png)

Dokument domyślny jest stroną sieci Web, która jest wyświetlana na głównym adresie URL witryny sieci Web. Używany jest pierwszy pasujący plik na liście. Aby dodać nowy dokument domyślny, kliknij pozycję **Nowy dokument**. Nie zapomnij kliknąć przycisk **Zapisz**.

Jeśli aplikacja używa modułów, które są oparte na adresie URL zamiast obsługiwać zawartość statyczną, nie ma potrzeby stosowania dokumentów domyślnych.

## <a name="configure-path-mappings"></a>Konfiguruj mapowania ścieżek

W [Azure Portal]przejdź do strony zarządzania aplikacją. W menu po lewej stronie aplikacji kliknij pozycję **konfiguracja** > **mapowania ścieżki**.

![Ustawienia ogólne](./media/configure-common/open-path.png)

Na stronie **mapowania ścieżki** są wyświetlane różne elementy w oparciu o typ systemu operacyjnego.

### <a name="windows-apps-uncontainerized"></a>Aplikacje systemu Windows (niekontenery)

W przypadku aplikacji systemu Windows można dostosować mapowania programu obsługi usług IIS i wirtualne aplikacje i katalogi.

Mapowania obsługi umożliwiają dodawanie niestandardowych procesorów skryptów do obsługi żądań dla określonych rozszerzeń plików. Aby dodać niestandardową procedurę obsługi, kliknij pozycję **Nowy program obsługi**. Skonfiguruj procedurę obsługi w następujący sposób:

- **Rozszerzenie**. Rozszerzenie pliku, które ma być obsługiwane, takie jak *\*. php* lub *Handler. FCGI*.
- **Procesor skryptu**. Ścieżka bezwzględna procesora skryptu do użytkownika. Żądania do plików, które pasują do rozszerzenia pliku, są przetwarzane przez procesor skryptów. Użyj ścieżki `D:\home\site\wwwroot`, aby odwołać się do katalogu głównego aplikacji.
- **Argumenty**. Opcjonalne argumenty wiersza polecenia dla procesora skryptów.

Każda aplikacja ma domyślną ścieżkę katalogu głównego (`/`) zamapowana na `D:\home\site\wwwroot`, w której kod jest wdrażany domyślnie. Jeśli katalog główny aplikacji znajduje się w innym folderze lub jeśli repozytorium zawiera więcej niż jedną aplikację, możesz w tym miejscu edytować lub dodać wirtualne aplikacje i katalogi. Kliknij pozycję **Nowa aplikacja wirtualna lub katalog**.

Aby skonfigurować aplikacje i katalogi wirtualne, określ każdy katalog wirtualny i odpowiadającą mu ścieżkę fizyczną względem katalogu głównego witryny sieci Web (`D:\home`). Opcjonalnie możesz zaznaczyć pole wyboru **aplikacji** , aby oznaczyć katalog wirtualny jako aplikację.

### <a name="containerized-apps"></a>Aplikacje w kontenerze

Możesz [dodać niestandardowy magazyn dla aplikacji w kontenerze](containers/how-to-serve-content-from-azure-storage.md). Aplikacje z kontenerami obejmują wszystkie aplikacje systemu Linux, a także kontenery niestandardowe z systemami Windows i Linux działające na App Service. Kliknij pozycję **Nowa instalacja usługi Azure Storage** i skonfiguruj własny magazyn w następujący sposób:

- **Nazwa**: Nazwa wyświetlana.
- **Opcje konfiguracji**: **podstawowa** lub **zaawansowana**.
- **Konta magazynu**: konto magazynu z żądanym kontenerem.
- **Typ magazynu**: **obiekty blob platformy Azure** lub **Azure Files**.
  > [!NOTE]
  > Aplikacje kontenera systemu Windows obsługują tylko Azure Files.
- **Kontener magazynu**: Konfiguracja podstawowa — żądany kontener.
- **Nazwa udziału**: w przypadku konfiguracji zaawansowanej nazwa udziału plików.
- **Klucz dostępu**: Aby skonfigurować konfigurację zaawansowaną, klucz dostępu.
- **Ścieżka instalacji**: ścieżka bezwzględna w kontenerze, w której ma zostać zainstalowany magazyn niestandardowy.

Aby uzyskać więcej informacji, zobacz temat [obsługiwanie zawartości z usługi Azure Storage w App Service w systemie Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurowanie ustawień stosu języka

W przypadku aplikacji systemu Linux Zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurowanie kontenerów niestandardowych

Zobacz [Konfigurowanie niestandardowego kontenera systemu Linux dla Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service]
- [Zabezpiecz niestandardową nazwę DNS z powiązaniem SSL w Azure App Service](configure-ssl-bindings.md)
- [Włączanie dzienników diagnostycznych](troubleshoot-diagnostic-logs.md)
- [Skalowanie aplikacji w Azure App Service]
- [Podstawowe informacje o monitorowaniu w Azure App Service]
- [Zmień ustawienia pliku applicationHost. config za pomocą programu applicationHost. XDT](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET sygnalizujący]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurowanie środowisk przejściowych w usłudze Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Podstawowe informacje o monitorowaniu w Azure App Service]: ./web-sites-monitor.md
[tryb potokowy]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji w Azure App Service]: ./manage-scale-up.md
