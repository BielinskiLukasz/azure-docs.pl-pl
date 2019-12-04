---
title: Przewodnik rozwiązywania problemów Eksplorator usługi Azure Storage | Microsoft Docs
description: Omówienie technik debugowania dla Eksplorator usługi Azure Storage
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d9967b6f1177281759dd66122ffd0183bb2b813d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775837"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów Eksplorator usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage jest aplikacją autonomiczną, która ułatwia współpracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux. Aplikacja może łączyć się z kontami magazynu hostowanymi na platformie Azure, chmurach narodowych i Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań dotyczących problemów, które są często spotykane w Eksplorator usługi Storage.

## <a name="rbac-permissions-issues"></a>Problemy z uprawnieniami RBAC

[Kontrola dostępu](https://docs.microsoft.com/azure/role-based-access-control/overview) oparta na rolach umożliwia wysoce szczegółowe zarządzanie zasobami platformy Azure przez łączenie zestawów uprawnień w _role_. Poniżej przedstawiono kilka strategii zapewniających optymalną pracę z usługą RBAC w Eksplorator usługi Storage.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Jak mogę uzyskać dostęp do moich zasobów w Eksplorator usługi Storage?

Jeśli masz problemy z uzyskaniem dostępu do zasobów magazynu za pomocą RBAC, być może nie masz przypisanych odpowiednich ról. W poniższych sekcjach opisano uprawnienia Eksplorator usługi Storage obecnie wymagane w celu uzyskania dostępu do zasobów magazynu. Jeśli nie masz pewności, czy masz odpowiednie role lub uprawnienia, skontaktuj się z administratorem konta platformy Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Odczyt: Lista/pobieranie kont magazynu" — problem z uprawnieniami

Musisz mieć uprawnienia do wyświetlania listy kont magazynu. Aby uzyskać to uprawnienie, musisz mieć przypisaną rolę _czytelnik_ .

#### <a name="list-storage-account-keys"></a>Wyświetl listę kluczy konta magazynu

Do uwierzytelniania żądań Eksplorator usługi Storage można także użyć kluczy konta. Dostęp do kluczy konta można uzyskać za pomocą bardziej zaawansowanych ról, takich jak rola _współautor_ .

> [!NOTE]
> Klucze dostępu udzielają nieograniczonych uprawnień osobom, które je przechowują. W związku z tym nie zalecamy przekazywania tych kluczy do użytkowników konta. Jeśli musisz odwołać klucze dostępu, możesz je wygenerować ponownie z [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role danych

Musisz mieć przypisaną co najmniej jedną rolę, która przyznaje dostęp do odczytu danych z zasobów. Jeśli na przykład chcesz wyświetlić lub pobrać obiekty blob, musisz mieć co najmniej rolę _czytnika danych obiektów blob magazynu_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego do wyświetlania moich zasobów w Eksplorator usługi Storage jest potrzebna rola warstwy zarządzania?

Usługa Azure Storage ma dwie warstwy dostępu: _Zarządzanie_ i _dane_. Do subskrypcji i kont magazynu uzyskuje się dostęp za pomocą warstwy zarządzania. Do kontenerów, obiektów blob i innych zasobów danych uzyskuje się dostęp za pomocą warstwy danych. Jeśli na przykład chcesz uzyskać listę kont magazynu z platformy Azure, Wyślij żądanie do punktu końcowego zarządzania. Jeśli potrzebujesz listy kontenerów obiektów BLOB na koncie, Wyślij żądanie do odpowiedniego punktu końcowego usługi.

Role RBAC mogą zawierać uprawnienia do zarządzania lub dostępu do warstwy danych. Rola czytelnik, na przykład, umożliwia dostęp tylko do odczytu do zasobów warstwy zarządzania.

Dokładnie mówiąc, rola czytelnika nie zapewnia żadnych uprawnień do warstwy danych i nie jest konieczna do uzyskania dostępu do warstwy danych.

Eksplorator usługi Storage ułatwia uzyskiwanie dostępu do zasobów, zbierając informacje niezbędne do nawiązania połączenia z zasobami platformy Azure. Na przykład aby wyświetlić kontenery obiektów blob, Eksplorator usługi Storage wysyła żądanie "list Containers" do punktu końcowego usługi BLOB Service. Aby uzyskać ten punkt końcowy, Eksplorator usługi Storage przeszukuje listę subskrypcji i kont magazynu, do których masz dostęp. Aby znaleźć subskrypcje i konta magazynu, Eksplorator usługi Storage wymaga również dostępu do warstwy zarządzania.

Jeśli nie masz roli przyznającej uprawnienia do warstwy zarządzania, Eksplorator usługi Storage nie może pobrać informacji potrzebnych do nawiązania połączenia z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uzyskać wymaganych uprawnień do warstwy zarządzania z mojego administratora?

Obecnie nie mamy rozwiązania dotyczącego kontroli RBAC dla tego problemu. Obejście tego problemu pozwala na zażądanie identyfikatora URI sygnatury dostępu współdzielonego w celu [dołączenia do zasobu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów zwykle występują w jednej z następujących sytuacji:

- Aplikacja jest połączona za pośrednictwem _przezroczystego serwera proxy_, co oznacza, że serwer (na przykład serwer firmy) przechwytuje ruch https, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.
- Korzystasz z aplikacji, która wprowadza certyfikat SSL z podpisem własnym do odbieranych komunikatów HTTPS. Przykładami aplikacji, które wprowadzają certyfikaty, są oprogramowanie antywirusowe i program inspekcji ruchu sieciowego.

Gdy Eksplorator usługi Storage widzi certyfikat z podpisem własnym lub niezaufany, nie wie, czy odebrany komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz poinstruować Eksplorator usługi Storage, aby go ufa, wykonując następujące czynności:

1. Uzyskaj kopię certyfikatu X. 509 z kodowaniem Base-64 (CER).
2. Przejdź do pozycji **edytuj** > **Certyfikaty SSL** > **zaimportować certyfikaty**, a następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć plik. cer.

Ten problem może również wystąpić, jeśli istnieje wiele certyfikatów (głównych i pośrednich). Aby naprawić ten błąd, należy dodać oba certyfikaty.

Jeśli nie masz pewności, skąd pochodzi certyfikat, wykonaj następujące kroki, aby je znaleźć:

1. Zainstaluj OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): wszystkie wersje oświetlenia powinny być wystarczające.
    * Komputery Mac i Linux: powinny być dołączone do systemu operacyjnego.
2. Uruchom OpenSSL.
    * Windows: Otwórz katalog instalacji, wybierz pozycję **/bin/** , a następnie kliknij dwukrotnie **plik OpenSSL. exe**.
    * Mac i Linux: uruchamianie `openssl` z terminalu.
3. Uruchom polecenie `s_client -showcerts -connect microsoft.com:443`.
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, które certyfikaty są z podpisem własnym, zanotuj miejsce, w którym `("s:")` podmiotu i wystawca `("i:")` są takie same.
5. Po znalezieniu certyfikatów z podpisem własnym dla każdego z nich skopiuj i Wklej wszystko z (i włącznie z) `-----BEGIN CERTIFICATE-----` za pośrednictwem `-----END CERTIFICATE-----` do nowego pliku. cer.
6. Otwórz Eksplorator usługi Storage i przejdź do pozycji **edytuj** > **Certyfikaty SSL** > **Importuj certyfikaty**. Następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć utworzone pliki CER.

Jeśli nie możesz znaleźć żadnych certyfikatów z podpisem własnym, wykonaj następujące kroki, aby skontaktować się z nami za pomocą narzędzia do przesyłania opinii. Możesz również otworzyć Eksplorator usługi Storage z wiersza polecenia przy użyciu flagi `--ignore-certificate-errors`. Po otwarciu z tą flagą Eksplorator usługi Storage ignoruje błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="blank-sign-in-dialog-box"></a>Puste okno dialogowe logowania

Puste okna dialogowe logowania najczęściej występują, gdy Active Directory Federation Services (AD FS) poprosi Eksplorator usługi Storage o przeprowadzenie przekierowania, który jest nieobsługiwany przez elektron. Aby obejść ten problem, możesz spróbować użyć przepływu kodu urządzenia do logowania. Aby to zrobić, wykonaj następujące kroki:

1. Na pasku narzędzi po lewej stronie, Otwórz pozycję **Ustawienia**. W panelu Ustawienia przejdź do pozycji **aplikacja** > **Zaloguj się**. Włącz **Logowanie za pomocą przepływu kodu urządzenia**.
2. Otwórz okno dialogowe **łączenie** (za pomocą ikony wtyczki na pasku pionowym po lewej stronie lub wybierając pozycję **Dodaj konto** w panelu konta).
3. Wybierz środowisko, do którego chcesz się zalogować.
4. Wybierz pozycję **Zaloguj**.
5. Postępuj zgodnie z instrukcjami wyświetlanymi na następnym panelu.

Jeśli nie możesz zalogować się do konta, którego chcesz użyć, ponieważ domyślna przeglądarka została już zarejestrowana na innym koncie, wykonaj jedną z następujących czynności:

- Ręcznie skopiuj link i kod do prywatnej sesji przeglądarki.
- Ręcznie skopiuj link i kod do innej przeglądarki.

### <a name="reauthentication-loop-or-upn-change"></a>Pętla ponownego uwierzytelniania lub zmiana nazwy UPN

Jeśli jesteś w pętli ponownego uwierzytelniania lub zmieniono nazwę UPN jednego z kont, wykonaj następujące czynności:

1. Usuń wszystkie konta, a następnie zamknij Eksplorator usługi Storage.
2. Usuń. IdentityService z komputera. W systemie Windows folder znajduje się w folderze `C:\users\<username>\AppData\Local`. W przypadku systemów Mac i Linux można znaleźć folder w katalogu głównym katalogu użytkownika.
3. W przypadku korzystania z systemu Mac lub Linux należy również usunąć wpis Microsoft. developer. IdentityService z magazynu kluczy używanego przez system operacyjny. Na komputerze Mac magazyn kluczy jest aplikacją GNOME z *łańcucha* . W systemie Linux aplikacja jest zazwyczaj nazywana _dzwonkiem_, ale nazwa może się różnić w zależności od dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy

Z powodu ograniczenia w bibliotece usługi Azure AD używanej przez Eksplorator usługi Storage dostęp warunkowy nie jest obsługiwany, gdy Eksplorator usługi Storage jest używany w systemie Windows 10, Linux lub macOS.

## <a name="mac-keychain-errors"></a>Błędy łańcucha kluczy Mac

MacOS pęku kluczy może czasami wprowadzić stan, który powoduje problemy z biblioteką uwierzytelniania Eksplorator usługi Storage. Aby uzyskać łańcuch kluczy z tego stanu, wykonaj następujące kroki:

1. Zamknij Eksplorator usługi Storage.
2. Otwórz pęku kluczy (naciśnij klawisze Command + Spacja, typ **pęku kluczy**i naciśnij klawisz ENTER).
3. Wybierz łańcuch kluczy logowania.
4. Wybierz ikonę kłódki, aby zablokować łańcucha kluczy. (Kłódka zostanie wyświetlona po zakończeniu procesu. Może to potrwać kilka sekund, w zależności od tego, jakie aplikacje zostały otwarte).

    ![Ikona kłódki](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otwórz Eksploratora usługi Storage.
6. Zostanie wyświetlony monit z komunikatem "Usługa Service Hub chce uzyskać dostęp do łańcucha kluczy". Wprowadź hasło konta administratora dla komputerów Mac i wybierz opcję **zawsze Zezwalaj** (lub **Zezwalaj** , jeśli **zawsze Zezwalaj** jest niedostępne).
7. Spróbuj się zalogować.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne kroki rozwiązywania problemów z logowaniem

* Jeśli jesteś w macOS, a okno logowania nigdy nie pojawia się w oknie dialogowym **oczekiwanie na uwierzytelnienie** , spróbuj wykonać [poniższe czynności](#mac-keychain-errors).
* Uruchom ponownie Eksplorator usługi Storage.
* Jeśli okno uwierzytelniania jest puste, odczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelnianie.
* Upewnij się, że ustawienia serwera proxy i certyfikatu zostały prawidłowo skonfigurowane dla komputera i Eksplorator usługi Storage.
* Jeśli używasz systemu Windows i masz dostęp do programu Visual Studio 2019 na tym samym komputerze i poświadczenia logowania, spróbuj zalogować się do programu Visual Studio 2019. Po pomyślnym zalogowaniu się do programu Visual Studio 2019 możesz otworzyć Eksplorator usługi Storage i zobaczyć swoje konto w panelu konta.

Jeśli żadna z tych metod nie zostanie zadziałała, [Otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brakujące subskrypcje i uszkodzone dzierżawy

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do oczekiwanych subskrypcji. Możesz sprawdzić dostęp, logując się do portalu dla środowiska platformy Azure, którego próbujesz użyć.
* Upewnij się, że zalogowano się za pomocą poprawnego środowiska platformy Azure (platformy Azure, platformy Azure z Chin, platformy Azure (Niemcy), platformy Azure dla instytucji rządowych USA lub środowiska niestandardowego.
* Jeśli jesteś za serwerem proxy, upewnij się, że poprawnie skonfigurowano serwer proxy Eksplorator usługi Storage.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli istnieje łącze "więcej informacji", sprawdź, które komunikaty o błędach są raportowane dla dzierżawców, które kończą się niepowodzeniem. Jeśli nie masz pewności, jak odpowiedzieć na komunikaty o błędach, możesz [otworzyć problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nie można usunąć dołączonego konta lub zasobu magazynu

Jeśli nie można usunąć dołączonego konta lub zasobu magazynu za pomocą interfejsu użytkownika, można ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksplorator usługi Storage przed usunięciem tych folderów.

> [!NOTE]
> Jeśli kiedykolwiek zaimportowano wszystkie certyfikaty SSL, wykonaj kopię zapasową zawartości katalogu `certs`. Później można użyć kopii zapasowej do zaimportowania certyfikatów SSL.

## <a name="proxy-issues"></a>Problemy z serwerem proxy

Najpierw upewnij się, że podane informacje są poprawne:

* Adres URL serwera proxy i numer portu
* Nazwa użytkownika i hasło, jeśli są wymagane przez serwer proxy

> [!NOTE]
> Eksplorator usługi Storage nie obsługuje plików autokonfiguracji serwera proxy na potrzeby konfigurowania ustawień serwera proxy.

### <a name="common-solutions"></a>Typowe rozwiązania

Jeśli nadal występują problemy, spróbuj wykonać następujące metody rozwiązywania problemów:

* Jeśli możesz nawiązać połączenie z Internetem bez użycia serwera proxy, sprawdź, czy Eksplorator usługi Storage działa bez włączonych ustawień serwera proxy. W takim przypadku może wystąpić problem z ustawieniami serwera proxy. Skontaktuj się z administratorem, aby zidentyfikować problemy.
* Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
* Sprawdź, czy możesz nawiązać połączenie z portalem dla środowiska platformy Azure, którego próbujesz użyć.
* Sprawdź, czy można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź jeden z adresów URL punktu końcowego do przeglądarki. Jeśli można nawiązać połączenie, należy odebrać InvalidQueryParameterValue lub podobną odpowiedź XML.
* Jeśli ktoś inny używa również Eksplorator usługi Storage z serwerem proxy, sprawdź, czy mogą się z nim połączyć. Jeśli to możliwe, może być konieczne skontaktowanie się z administratorem serwera proxy.

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Jeśli masz narzędzia sieciowe, takie jak programu Fiddler for Windows, możesz zdiagnozować problemy w następujący sposób:

* Jeśli trzeba będzie korzystać z serwera proxy, może być konieczne skonfigurowanie narzędzia sieci do łączenia się z serwerem proxy.
* Sprawdź numer portu używany przez narzędzie sieciowe.
* Wprowadź adres URL hosta lokalnego i numer portu Narzędzia sieciowego jako ustawienia serwera proxy w Eksplorator usługi Storage. Po poprawnym wykonaniu tego narzędzia sieciowego rozpocznie rejestrowanie żądań sieci, które zostały wprowadzone przez Eksplorator usługi Storage do punktów końcowych zarządzania i usług. Na przykład wprowadź `https://cawablobgrs.blob.core.windows.net/` dla punktu końcowego obiektu BLOB w przeglądarce i otrzymasz odpowiedź podobną do następującej:

  ![Przykład kodu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Ta odpowiedź sugeruje, że zasób istnieje, nawet jeśli nie można uzyskać do niego dostępu.

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, może być konieczne skontaktowanie się z administratorem serwera proxy, aby:

* Upewnij się, że serwer proxy nie blokuje ruchu sieciowego do zarządzania platformą Azure lub punktów końcowych zasobów.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage nie obsługuje obecnie proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "nie można pobrać elementu podrzędnego"

Jeśli nawiązano połączenie z platformą Azure za pomocą serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne. Jeśli masz udzielony dostęp do zasobu od właściciela subskrypcji lub konta, sprawdź, czy masz uprawnienia do odczytu lub listy dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametry połączenia nie mają pełnych ustawień konfiguracji

Jeśli zostanie wyświetlony komunikat o błędzie, istnieje możliwość, że nie masz wystarczających uprawnień, aby uzyskać klucze dla konta magazynu. Aby upewnić się, że jest to przypadek, przejdź do portalu i Znajdź swoje konto magazynu. Aby to zrobić, kliknij prawym przyciskiem myszy węzeł konta magazynu i wybierz polecenie **Otwórz w portalu**. Następnie przejdź do bloku **klucze dostępu** . Jeśli nie masz uprawnień do wyświetlania kluczy, zobaczysz komunikat "nie masz dostępu". Aby obejść ten problem, możesz uzyskać klucz konta od kogoś innego i dołączyć nazwę i klucz lub poprosił kogoś o sygnaturę dostępu współdzielonego z kontem magazynu i użyć go do dołączenia konta magazynu.

Jeśli widzisz klucze konta, w usłudze GitHub prosimy o problem, aby pomóc w rozwiązaniu problemu.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Wystąpił błąd podczas dodawania nowego połączenia: TypeError: nie można odczytać właściwości "wersja" niezdefiniowanej

Jeśli podczas próby dodania połączenia niestandardowego zostanie wyświetlony komunikat o błędzie, dane połączenia przechowywane w lokalnym Menedżerze poświadczeń mogą być uszkodzone. Aby obejść ten problem, spróbuj usunąć uszkodzone połączenia lokalne, a następnie dodaj je ponownie:

1. Rozpocznij Eksplorator usługi Storage. W menu Przejdź do okna **pomoc** > **Przełącz narzędzia deweloperskie**.
2. W otwartym oknie na karcie **aplikacja** przejdź do obszaru **Magazyn lokalny** (po lewej stronie) > **File://** .
3. W zależności od typu połączenia, z którym występuje problem, poszukaj jego klucza, a następnie skopiuj jego wartość do edytora tekstu. Wartość jest tablicą niestandardowych nazw połączeń, takich jak następujące:
    * Konta magazynu
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontenery obiektów blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Udziały plików
        * `StorageExplorer_CustomConnections_Files_v1`
    * Kolejki
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabele
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po zapisaniu bieżących nazw połączeń ustaw wartość w Narzędzia deweloperskie na `[]`.

Aby zachować połączenia, które nie są uszkodzone, można użyć poniższych kroków w celu zlokalizowania uszkodzonych połączeń. Jeśli nie chcesz utracić wszystkich istniejących połączeń, możesz pominąć te kroki i postępować zgodnie z instrukcjami dotyczącymi konkretnej platformy, aby wyczyścić dane połączenia.

1. W edytorze tekstów ponownie Dodaj nazwy poszczególnych połączeń do Narzędzia deweloperskie, a następnie sprawdź, czy połączenie nadal działa.
2. Jeśli połączenie działa prawidłowo, nie jest uszkodzone i można je bezpiecznie pozostawić. Jeśli połączenie nie działa, usuń jego wartość z Narzędzia deweloperskie i Zapisz je tak, aby można było dodać je ponownie później.
3. Powtarzaj do momentu zbadania wszystkich połączeń.

Po przejściu przez wszystkie połączenia w przypadku wszystkich nazw połączeń, które nie zostały dodane z powrotem, należy wyczyścić ich uszkodzone dane (jeśli istnieją) i dodać je z powrotem przy użyciu standardowych kroków w Eksplorator usługi Storage:

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. W menu **Start** Wyszukaj pozycję **Menedżer poświadczeń** i otwórz ją.
2. Przejdź do **poświadczeń systemu Windows**.
3. W obszarze **poświadczenia ogólne**poszukaj wpisów mających klucz `<connection_type_key>/<corrupted_connection_name>` (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1`).
4. Usuń te wpisy i Dodaj je jeszcze raz.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Otwórz centrum uwagi (Command + SPACEBAR) i Wyszukaj **dostęp do łańcucha kluczy**.
2. Poszukaj wpisów mających klucz `<connection_type_key>/<corrupted_connection_name>` (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Usuń te wpisy i Dodaj je jeszcze raz.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Lokalne zarządzanie poświadczeniami różni się w zależności od dystrybucji systemu Linux. Jeśli dystrybucja systemu Linux nie udostępnia wbudowanego interfejsu GUI do lokalnego zarządzania poświadczeniami, możesz zainstalować narzędzie innych firm, aby zarządzać poświadczeniami lokalnymi. Na przykład można użyć [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), narzędzia interfejsu GUI open source do zarządzania poświadczeniami lokalnymi systemu Linux.

1. Otwórz swoje lokalne narzędzie do zarządzania poświadczeniami i Znajdź zapisane poświadczenia.
2. Poszukaj wpisów mających klucz `<connection_type_key>/<corrupted_connection_name>` (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Usuń te wpisy i Dodaj je jeszcze raz.
---

Jeśli ten błąd będzie nadal występował po wykonaniu tych kroków lub jeśli chcesz udostępnić podejrzane informacje, zapoznaj [się z informacjami na naszej](https://github.com/microsoft/AzureStorageExplorer/issues) stronie usługi GitHub.

## <a name="issues-with-sas-url"></a>Problemy z adresem URL SAS

Jeśli łączysz się z usługą za pomocą adresu URL sygnatury dostępu współdzielonego i wystąpił błąd:

* Sprawdź, czy adres URL zawiera niezbędne uprawnienia do odczytu lub wyświetlania zasobów.
* Sprawdź, czy adres URL nie wygasł.
* Jeśli adres URL SAS jest oparty na zasadach dostępu, sprawdź, czy zasady dostępu nie zostały odwołane.

Jeśli przypadkowo dołączono przy użyciu nieprawidłowego adresu URL sygnatury dostępu współdzielonego i teraz nie można odłączyć, wykonaj następujące kroki:

1. Gdy korzystasz z programu Eksplorator usługi Storage, naciśnij klawisz F12, aby otworzyć okno Narzędzia deweloperskie.
2. Na karcie **aplikacja** wybierz pozycję **magazyn lokalny** > **File://** w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematycznego identyfikatora URI sygnatury dostępu współdzielonego. Na przykład jeśli zły identyfikator URI sygnatury dostępu współdzielonego dotyczy kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z nieprawidłowym identyfikatorem URI, a następnie usuń go.
5. Naciśnij klawisze CTRL + R, aby ponownie załadować Eksplorator usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

Eksplorator usługi Storage 1.10.0 i nowsze są dostępne jako Przyciągaj z magazynu Snap. Przystawka Eksplorator usługi Storage automatycznie instaluje wszystkie zależności i jest aktualizowana, gdy dostępna jest nowa wersja przyciągania. Zalecaną metodą instalacji jest zainstalowanie przystawki Eksplorator usługi Storage.

Eksplorator usługi Storage wymaga użycia Menedżera haseł, który może być konieczne nawiązanie połączenia ręcznie, zanim Eksplorator usługi Storage będzie działał poprawnie. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Możesz również pobrać aplikację jako plik tar. gz, ale musisz ręcznie zainstalować zależności.

> [!IMPORTANT]
> Eksplorator usługi Storage, jak to podano w pobraniu elementu. tar. gz, jest obsługiwana tylko dla dystrybucji Ubuntu. Inne dystrybucje nie zostały zweryfikowane i mogą wymagać alternatywnych lub dodatkowych pakietów.

Te pakiety stanowią najczęstsze wymagania dotyczące Eksplorator usługi Storage w systemie Linux:

* [Środowisko uruchomieniowe programu .NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` lub `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Eksplorator usługi Storage wersja 1.7.0 i wcześniejsze wymagają programu .NET Core 2,0. Jeśli masz zainstalowaną nowszą wersję programu .NET Core, musisz [zastosować poprawkę Eksplorator usługi Storage](#patching-storage-explorer-for-newer-versions-of-net-core). Jeśli korzystasz z programu Eksplorator usługi Storage 1.8.0 lub nowszego, powinno być możliwe użycie do .NET Core 2,2. W tej chwili nie zweryfikowano wersji ponad 2,2.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19,04](#tab/1904)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16,04](#tab/1604)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14,04](#tab/1404)

1. Pobierz Eksplorator usługi Storage.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Uruchom następujące polecenie:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Stosowanie poprawek Eksplorator usługi Storage w przypadku nowszych wersji platformy .NET Core

W przypadku Eksplorator usługi Storage 1.7.0 lub starszych może być konieczne zainstalowanie wersji programu .NET Core używanej przez Eksplorator usługi Storage:

1. Pobierz wersję 1.5.43 z StreamJsonRpc [z narzędzia NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Poszukaj linku "Pobierz pakiet" po prawej stronie strony.
2. Po pobraniu pakietu zmień jego rozszerzenie na `.nupkg` na `.zip`.
3. Rozpakuj pakiet.
4. Otwórz folder `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Skopiuj `StreamJsonRpc.dll` do następujących lokalizacji w folderze Eksplorator usługi Storage:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Otwórz w Eksploratorze" z Azure Portal nie działa

Jeśli przycisk **Otwórz w Eksploratorze** na Azure Portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Następujące przeglądarki zostały przetestowane pod kątem zgodności:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Program Microsoft Internet Explorer

## <a name="next-steps"></a>Następne kroki

Jeśli żadne z tych rozwiązań nie zadziałało, [Otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz to zrobić, wybierając przycisk **Zgłoś problem do serwisu GitHub** w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)
