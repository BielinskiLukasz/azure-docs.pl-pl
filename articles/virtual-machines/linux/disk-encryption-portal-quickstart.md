---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć i zaszyfrować maszynę wirtualną z systemem Linux.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 9f09f28dc63e7f061946a66beb59bd4c62be70aa
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970557"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej przy użyciu Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku szybki start użyjesz Azure Portal, aby wdrożyć maszynę wirtualną z systemem Linux, na której działa Ubuntu 18,04 LTS, utworzyć magazyn kluczy na potrzeby przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Azure portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. Na stronie nowy w obszarze popularne wybierz pozycję **Ubuntu Server 18,04 LTS**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów**wybierz grupę zasobów, która została utworzona podczas tworzenia magazynu kluczy powyżej (np. Grupa **zasobów).**
1. W obszarze **Nazwa maszyny wirtualnej**wprowadź *MyVM*.
1. W polu **region**wybierz ten sam region, który został użyty podczas tworzenia magazynu kluczy (np. **Wschodnie stany USA**).
1. Upewnij się, że **rozmiar** jest *Standardowy D2s v3*.
1. W obszarze **konto administratora**wybierz pozycję **hasło**. Wprowadź nazwę użytkownika i hasło.
    ![ekranu tworzenia z przetworzeniem zasobów](./media/disk-encryption/portal-qs-vm-creation.png)
1. Wybierz kartę Zarządzanie i sprawdź, czy masz konto magazynu diagnostyki. Jeśli nie masz żadnych kont magazynu, wybierz pozycję "Utwórz nowe", Nadaj nowemu kontu nazwę, a następnie wybierz przycisk "OK" ![ekranu tworzenia podsieci](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kliknij pozycję "Przejrzyj + Utwórz".
1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

1. Po zakończeniu wdrożenia maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.
1. Na pasku bocznym po lewej stronie wybierz pozycję **dyski**.
1. Na ekranie dyski wybierz pozycję **szyfrowanie**. 

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na ekranie szyfrowanie w obszarze **dyski do zaszyfrowania**wybierz opcję **dyski systemu operacyjnego i danych**.
1. W obszarze **Ustawienia szyfrowania**wybierz **pozycję Wybierz magazyn kluczy i klucz do szyfrowania**.
1. Na ekranie **Wybieranie klucza z Azure Key Vault** wybierz pozycję **Utwórz nowy**.

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na ekranie **Tworzenie magazynu kluczy** upewnij się, że grupa zasobów jest taka sama jak ta, która została użyta do utworzenia maszyny wirtualnej.
1. Podaj nazwę magazynu kluczy.  Każdy Magazyn kluczy na platformie Azure musi mieć unikatową nazwę.
1. Na karcie **zasady dostępu** sprawdź pole **Azure Disk Encryption do szyfrowania woluminów** .

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Wybierz pozycję **Przegląd + utwórz**.  
1. Po przekazaniu walidacji magazynu kluczy wybierz pozycję **Utwórz**. Spowoduje to powrót do ekranu **wyboru z Azure Key Vault** .
1. Pozostaw pole **klucza** puste i wybierz **pozycję Wybierz**.
1. W górnej części ekranu szyfrowania kliknij przycisk **Zapisz**. W oknie podręcznym zostanie wyświetlone ostrzeżenie o ponownym uruchomieniu maszyny wirtualnej. Kliknij przycisk **Yes** (Tak).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów dla maszyny wirtualnej, wybierz pozycję Usuń, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault, dla których włączono obsługę kluczy szyfrowania, utworzono maszynę wirtualną i włączono maszynę wirtualną do szyfrowania.  

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
