---
title: 'Usługa Azure Active Directory Domain Services: Dołącz do maszyny Wirtualnej z systemem CentOS do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołączanie maszyny wirtualnej systemu CentOS Linux w usłudze Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: cfa0ab1fdf72e3d363b5845c8602b0bea7ff6a12
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155188"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Dołączanie maszyny wirtualnej systemu CentOS Linux do domeny zarządzanej
W tym artykule pokazano, jak dołączyć maszyny wirtualnej systemu CentOS Linux na platformie Azure do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:
1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](active-directory-ds-getting-started.md).
4. Upewnij się, że zostały skonfigurowane adresy IP w domenie zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej z systemem Linux CentOS
Aprowizowanie maszyny wirtualnej CentOS na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ta, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Połącz się zdalnie nowo aprowizowanej maszyny wirtualnej systemu Linux
Maszyny wirtualne CentOS zainicjowano na platformie Azure. Kolejnym krokiem jest zdalne ustanawianie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego, utworzony podczas aprowizowania maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hosts na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwa hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następującą wartość:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS z domeny zarządzanej. "contoso-centos" jest nazwą hosta maszyny wirtualnej CentOS, z którą następuje przyłączanie do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalowanie wymaganych pakietów na maszynie wirtualnej systemu Linux
Następnie zainstaluj pakiety wymagane do przyłączania do domeny na maszynie wirtualnej. W terminalu SSH wpisz następujące polecenie, aby zainstalować wymagane pakiety:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Dołączanie maszyny wirtualnej systemu Linux do domeny zarządzanej
Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, kolejnym krokiem jest można dołączyć maszyny wirtualnej do domeny zarządzanej.

1. Odkryj domeny zarządzanej usług domenowych w usłudze AAD. W terminalu SSH wpisz następujące polecenie:

    ```
    sudo realm discover CONTOSO100.COM
    ```

    > [!NOTE]
    > **Rozwiązywanie problemów:** Jeśli *odnajdywania obszaru* nie może znaleźć Twojej domeny zarządzanej:  
      * Upewnij się, że domena jest dostępny z maszyny wirtualnej (spróbuj ping).  
      * Sprawdź, czy maszyna wirtualna w rzeczywistości został wdrożony na tej samej sieci wirtualnej, w którym domena zarządzana jest dostępna.
      * Sprawdź, czy zostały zaktualizowane ustawienia serwera DNS dla sieci wirtualnej, aby wskazywała na kontrolerach domeny z domeny zarządzanej.  
      >

2. Zainicjuj protokołu Kerberos. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > * Określ użytkownika, który należy do grupy "Administratorzy usługi AAD DC".
    > * Określ nazwę domeny wielkimi literami, narzędzie kinit w przyłączonej jeszcze nie powiedzie się.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Przyłączanie komputera do domeny. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > Użyj tego samego konta użytkownika, które określiłeś w poprzednim kroku (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Powinien zostać wyświetlony komunikat ("zarejestrowano pomyślnie maszyny w obszarze") po komputer pomyślnie jest przyłączony do domeny zarządzanej.


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy komputer został pomyślnie dołączony do domeny zarządzanej. Nawiązać CentOS przyłączonych do domeny maszyny Wirtualnej przy użyciu innego połączenia SSH. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozwiązany.

1. Twoje terminala protokołu SSH wpisz następujące polecenie, aby połączyć się z domeną przyłączony CentOS maszyny wirtualnej przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli katalog macierzysty został poprawnie zainicjowany.
    ```
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli w grupach są rozpoznawane poprawnie.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączania do domeny
Zapoznaj się [przyłączania do domeny Rozwiązywanie problemów](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artykułu.

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](active-directory-ds-getting-started.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalowanie protokołu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 — przewodnik integracji Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
