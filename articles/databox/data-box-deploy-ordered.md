---
title: Zamawianie urządzenia Microsoft Azure Data Box | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących wdrażania oraz sposobie zamawiania urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 07ccd2aaec6b2325d6eef09a466a5d0707836b4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834272"
---
# <a name="tutorial-order-azure-data-box"></a>Samouczek: zamawianie urządzenia Azure Data Box

Azure Data Box to hybrydowe rozwiązanie, które umożliwia importowanie danych lokalnych na platformę Azure szybko, łatwo i bez problemów. Przesyłasz swoje dane na urządzenie magazynujące dostarczane przez firmę Microsoft, którego pojemność do wykorzystania to 80 TB, a następnie odsyłasz urządzenie z powrotem do firmy Microsoft. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania usługi Azure Data Box. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie usługi Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z usługą i urządzeniem Data Box.

### <a name="for-service"></a>Na potrzeby usługi

Przed rozpoczęciem upewnij się, że:
- Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Sprawdź, czy masz dostęp na poziomie właściciela lub współautora do subskrypcji, aby móc utworzyć zamówienie urządzenia Data Box.

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:
- Należy posiadać komputer hosta podłączony do sieci centrum danych. Za pomocą urządzenia Data Box zostaną skopiowane dane z tego komputera. Na komputerze hosta musi działać obsługiwany system operacyjny, zgodnie z opisem w [wymaganiach dotyczących systemu Azure Data Box](data-box-system-requirements.md).
- Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania.


## <a name="order-data-box"></a>Zamawianie urządzenia Data Box

Wykonaj poniższe czynności w witrynie Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Kliknij pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Kliknij pozycję **Azure Data Box**.
    
   [![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kliknij pozycję **Utwórz**.

4. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje i kliknij pozycję **Zastosuj**. 

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Kraj źródłowy     |   Wybierz kraj, w którym aktualnie znajdują się dane.         |
    |Docelowy region platformy Azure     |     Wybierz region platformy Azure, do którego chcesz przenieść dane.        |

5. Wybierz pozycję **Data Box**. Maksymalnej pojemności do wykorzystania dla pojedynczego zamówienia jest 786 TB. W przypadku większych ilości danych możesz utworzyć wiele zamówień.

      [![Wybieranie opcji 1 urządzenia Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje, a następnie kliknij pozycję **Dalej**.
    
    |Ustawienie  |Wartość  |
    |---------|---------|
    |Name (Nazwa)     |  Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |
    |Grupa zasobów     |   Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Docelowy region platformy Azure     | Wybierz region swojego konta magazynu. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](data-box-overview.md#region-availability).        |
    |Magazyn docelowy     | Wybierz z konta magazynu dysków zarządzanych i/lub. <br> W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. Urządzenie Data Box można połączyć z maksymalnie 10 kontami magazynu. <br> Można również utworzyć nową **General-purpose v1**, **ogólnego przeznaczenia v2**, lub **konta magazynu obiektów Blob**. <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby usługa Data Box do pracy z kontami magazynu zabezpieczone, Włącz zaufanych usług w ramach ustawienia zapory sieci konta magazynu. Aby uzyskać więcej informacji, zobacz instrukcje [Dodaj urządzenia Azure Data Box jako zaufaną usługę](../storage/common/storage-network-security.md#exceptions).|

    Jeśli jako miejsce docelowe przechowywania, przy użyciu konta magazynu, zostanie wyświetlony poniższy zrzut ekranu:

    ![Kolejność pole danych dla konta magazynu](media/data-box-deploy-ordered/order-storage-account.png)

    Jeśli tworzenie dysków zarządzanych z wirtualnych dysków twardych w środowisku lokalnym za pomocą urządzenia Data Box, należy również podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Tworzenie nowych grup zasobów, jeśli zamierzasz utworzyć dysków zarządzanych z wirtualnych dysków twardych w środowisku lokalnym. Można użyć istniejącej grupy zasobów, tylko wtedy, gdy grupa zasobów została utworzona wcześniej podczas tworzenia zamówienie usługi Data Box dla dysku zarządzanego przez usługę Data Box. <br> Określ wiele grup zasobów, rozdzielając je średnikami. Obsługiwane są maksymalnie 10 grup zasobów.|

    ![Kolejność pole danych dla dysku zarządzanego](media/data-box-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako konta magazynu przejściowego. Przekazywanie usługi Data Box, pliki VHD jako obiekty BLOB konta magazynu przejściowego przed konwersją do dysków zarządzanych i przeniesienie ich do grup zasobów. Aby uzyskać więcej informacji, zobacz [danych sprawdź, czy przekazać na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Kliknij pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie. Kliknij przycisk **Dalej**.

8. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

9. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

10. Kliknij pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.


## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia urządzenia Data Box, a następnie przejdź do obszaru **Omówienie**, aby sprawdzić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Jeśli urządzenie nie jest niedostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

- Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
- Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
- Generowane jest również hasło urządzenia, które pomaga odblokować urządzenie.
- Urządzenie Data Box jest blokowane w celu uniemożliwienia nieupoważnionego dostępu do urządzenia w dowolnym momencie.

Po zakończeniu przygotowywania urządzenia w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

![Przetworzone zamówienie urządzenia Data Box](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Wysłane zamówienie urządzenia Data Box](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować to zamówienie, w witrynie Azure Portal przejdź do obszaru **Przegląd**, a następnie kliknij pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.
 
Aby usunąć anulowane zamówienie, w obszarze **Omówienie** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie urządzenia Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box](./data-box-deploy-set-up.md)


