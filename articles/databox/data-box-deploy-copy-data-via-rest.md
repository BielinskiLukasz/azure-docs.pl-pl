---
title: Kopiowanie danych do magazynu obiektów blob usługi Azure Data Box za pośrednictwem interfejsów API REST | Microsoft Docs
description: Dowiedz się, jak skopiować dane do magazynu obiektów blob usługi Azure Data Box za pośrednictwem interfejsów API REST
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: fa0883c18e0d586e6e9b35fb19dbe304388045b5
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215290"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Samouczek: kopiowanie danych do magazynu obiektów blob usługi Azure Data Box za pośrednictwem interfejsów API REST  

W tym samouczku opisano procedury łączenia się z magazynem obiektów blob usługi Azure Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS* przy użyciu interfejsów API REST. Opisano także czynności, które należy wykonać po nawiązaniu połączenia w celu skopiowania danych do magazynu obiektów blob usługi Data Box i przygotowania urządzenia Data Box do wysyłki.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie się z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*
> * Kopiowanie danych na urządzenie Data Box
> * Przygotowanie do wysłania

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: konfigurowanie usługi Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Znasz [wymagania systemowe magazynu obiektów blob usługi Data Box](data-box-system-requirements-rest.md) oraz obsługiwane wersje interfejsów API, zestawów SDK i narzędzi.
4. Masz dostęp do komputera-hosta zawierającego dane, które mają zostać skopiowane do usługi Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania.
5. [Pobierz narzędzie AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) na komputer hosta. Użyjesz narzędzia AzCopy do skopiowania danych z komputera hosta do magazynu obiektów blob usługi Azure Data Box.


## <a name="connect-to-data-box-blob-storage"></a>Łączenie się z magazynem obiektów blob usługi Data Box

Możesz nawiązać połączenie z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*. Mówiąc ogólnie, protokół *HTTPS* to bezpieczny, zalecany sposób nawiązywania połączenia z magazynem obiektów blob usługi Data Box. Protokół *HTTP* jest używany, gdy połączenie jest nawiązywane za pośrednictwem zaufanych sieci. W zależności od tego, czy łączysz się z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* czy *HTTPS*, może być wymagane wykonanie innych czynności.

## <a name="connect-via-http"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTP

Aby nawiązać połączenie z interfejsami API REST magazynu obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP*, należy wykonać następujące czynności:

- Dodawanie adresu IP urządzenia i punktu końcowego usługi obiektów blob do hosta zdalnego
- Konfigurowanie oprogramowania innych firm i weryfikowanie połączenia

Wszystkie wymienione kroki zostały opisane poniżej.

#### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Dodawanie adresu IP urządzenia i punktu końcowego usługi obiektów blob do hosta zdalnego

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

#### <a name="configure-partner-software-and-verify-connection"></a>Konfigurowanie oprogramowania partnerów i weryfikowanie połączenia

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTPS

Aby nawiązać połączenie z interfejsami API REST usługi Azure Blob Storage za pośrednictwem protokołu HTTPS, należy wykonać następujące czynności:

- Pobieranie certyfikatu z witryny Azure Portal
- Przygotowywanie komputera hosta do zarządzania zdalnego
- Dodawanie adresu IP urządzenia i punktu końcowego usługi obiektów blob do hosta zdalnego
- Konfigurowanie oprogramowania innych firm i weryfikowanie połączenia

Wszystkie wymienione kroki zostały opisane poniżej.

### <a name="download-certificate"></a>Pobieranie certyfikatu

Pobierz certyfikat z witryny Azure Portal.

1. Zaloguj się do witryny Azure Portal.
2. Przejdź do zamówienia na urządzenie Data Box, a następnie wybierz pozycję **Ogólne > Szczegóły urządzenia**.
3. W obszarze **Poświadczenia urządzenia** przejdź do sekcji **Dostęp za pomocą interfejsu API**. Kliknij pozycję **Pobierz**. To spowoduje pobranie pliku certyfikatu o nazwie **<your order name>.cer**. **Zapisz** ten plik. Ten certyfikat zainstalujesz na komputerze klienta lub hosta, którego będziesz używać do nawiązania połączenia z urządzeniem.

    ![Pobieranie certyfikatu z witryny Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="prepare-the-host-for-remote-management"></a>Przygotowywanie hosta do zarządzania zdalnego

Wykonaj następujące czynności, aby przygotować klienta z systemem Windows do nawiązania połączenia zdalnego z użyciem sesji protokołu *HTTPS*:

- Zaimportuj plik cer do magazynu głównego klienta lub hosta zdalnego.
- Dodaj adres IP urządzenia oraz punkt końcowy usługi obiektów blob w pliku hosts na komputerze klienckim z systemem Windows.

Poniżej opisano każdą z tych procedur.

#### <a name="import-the-certificate-on-the-remote-host"></a>Importowanie certyfikatu na zdalnym hoście

Aby zaimportować i zainstalować certyfikat w systemie hosta, można użyć programu Windows PowerShell lub interfejsu użytkownika systemu Windows Server.

**Korzystanie z programu PowerShell**

1. Uruchom sesję programu Windows PowerShell jako administrator.
2. W wierszu polecenia wpisz polecenie:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

**Korzystanie z interfejsu użytkownika systemu Windows Server**

1.  Kliknij prawym przyciskiem myszy plik cer, a następnie wybierz pozycję **Zainstaluj certyfikat**. Spowoduje to uruchomienie Kreatora importu certyfikatów.
2.  W polu **Lokalizacja magazynu** wybierz pozycję **Maszyna lokalna**, a następnie kliknij przycisk **Dalej**.

    ![Importowanie certyfikatu przy użyciu programu PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do głównego magazynu hosta zdalnego, a następnie kliknij przycisk **Dalej**.

    ![Importowanie certyfikatu przy użyciu programu PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Kliknij przycisk **Zakończ**. Pojawi się komunikat informujący, że importowanie zakończyło się pomyślnie.

    ![Importowanie certyfikatu przy użyciu programu PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

### <a name="to-add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Aby dodać adres IP urządzenia i punkt końcowy usługi obiektów blob do hosta zdalnego

Należy wykonać takie same czynności, jak w przypadku nawiązywania połączenia za pośrednictwem protokołu *HTTP*.

### <a name="configure-partner-software-to-establish-connection"></a>Konfigurowanie oprogramowania partnerów w celu nawiązania połączenia

Należy wykonać takie same czynności, jak w przypadku nawiązywania połączenia za pośrednictwem protokołu *HTTP*. Jedyną różnicą jest to, że należy pozostawić pole *Użyj protokołu HTTP* niezaznaczone.

## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z magazynem obiektów blob usługi Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Azure storage and Data Box limits](data-box-limits.md) (Ograniczenia usług Azure Storage i urządzenia Data Box).
- Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.

W tym samouczku użyjesz narzędzia AzCopy do skopiowania danych do magazynu obiektów blob usługi Data Box. Możesz również skopiować dane za pomocą Eksploratora usługi Azure Storage (jeśli wolisz użyć narzędzia z graficznym interfejsem użytkownika) lub oprogramowania partnera.
Procedura kopiowania obejmuje następujące czynności:

- Tworzenie kontenera
- Przekazywanie zawartości folderu do magazynu obiektów blob usługi Data Box
- Przekazywanie zmodyfikowanych plików do magazynu obiektów blob usługi Data Box

Wszystkie wymienione kroki zostały szczegółowo opisane poniżej.

### <a name="create-a-container"></a>Tworzenie kontenera

Pierwszym krokiem jest utworzenie kontenera, ponieważ obiekty blob są zawsze przekazywane do kontenera. Kontenery umożliwiają organizowanie grup obiektów blob w sposób podobny do organizowania plików w folderach na komputerze. Wykonaj następujące czynności, aby utworzyć kontener obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy pozycję **Kontenery obiektów Blob** i wybierz pozycję **Utwórz kontener obiektów Blob** z menu kontekstowego.

   ![Menu kontekstowe — tworzenie kontenerów obiektów blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Poniżej folderu **Kontenery obiektów Blob** będzie widoczne pole tekstowe. Wprowadź nazwę kontenera obiektów blob. Informacje na temat reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów blob znajdują się w sekcji [Tworzenie kontenera i ustawianie uprawnień](../storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions).
5. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob, lub klawisz **Esc**, aby anulować. Po pomyślnym utworzeniu kontener obiektów blob zostanie wyświetlony w folderze **Kontenery obiektów blob** na wybranym koncie magazynu.

   ![Utworzony kontener obiektów blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Przekazywanie zawartości folderu do magazynu obiektów blob usługi Data Box

Użyj narzędzia AzCopy, aby przekazać wszystkie pliki w folderze do magazynu obiektów blob w systemie Windows lub Linux. Aby przekazać wszystkie obiekty blob w folderze, wprowadź następujące polecenie narzędzia AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Zastąp wartość `<key>` własnym kluczem konta. Aby znaleźć klucz konta, w witrynie Azure Portal przejdź do swojego konta magazynu. Przejdź do pozycji **Ustawienia > Klucze dostępu**, a następnie zaznacz klucz i wklej go do polecenia narzędzia AzCopy.

Jeśli określony kontener docelowy nie istnieje, narzędzie AzCopy utworzy go i przekaże do niego plik. Zmień ścieżkę źródłową na Twój katalog danych i zastąp wartość `data-box-storage-account-name` w docelowym adresie URL nazwą konta magazynu skojarzonego z Twoją usługą Data Box.

Aby przekazać zawartość określonego katalogu do magazynu obiektów blob w sposób rekursywny, określ opcję `--recursive` (system Linux) lub `/S` (system Windows). Po uruchomieniu narzędzia AzCopy z jedną z tych opcji wszystkie podfoldery i znajdujące się w nich pliki również zostaną przekazane.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Przekazywanie zmodyfikowanych plików do magazynu obiektów blob usługi Data Box

Użyj narzędzia AzCopy do przekazania plików na podstawie daty ich ostatniej modyfikacji. Aby z tego skorzystać, zmodyfikuj pliki lub utwórz nowe pliki w katalogu źródłowym do celów testowych. Aby przekazać tylko zaktualizowane lub nowe pliki, do polecenia narzędzia AzCopy dodaj parametr `--exclude-older` (system Linux) lub `/XO` (system Windows).

Jeśli chcesz skopiować tylko zasoby źródłowe, które nie istnieją w miejscu docelowym, określ zarówno parametry `--exclude-older` i `--exclude-newer` (system Linux) lub `/XO` i `/XN` (system Windows) w poleceniu narzędzia AzCopy. Narzędzie AzCopy przekazuje tylko zaktualizowane dane na podstawie ich sygnatury czasowej.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO


## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Łączenie się z magazynem obiektów blob usługi Data Box za pośrednictwem protokołu *HTTP* lub *HTTPS*
> * Kopiowanie danych na urządzenie Data Box
> * Przygotowanie do wysłania

Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-picked-up.md)