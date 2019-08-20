---
title: Samouczek — Tworzenie maszyny wirtualnej zarządzania dla Azure Active Directory Domain Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować maszynę wirtualną z systemem Windows, która będzie używana do administrowania wystąpieniem Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619096"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Samouczek: Tworzenie maszyny wirtualnej zarządzania w celu skonfigurowania i administrowania Azure Active Directory Domain Services domeną zarządzaną

Azure Active Directory Domain Services (AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Administrujesz tą domeną zarządzaną przy użyciu tego samego Narzędzia administracji zdalnej serwera (RSAT) jak z lokalną domeną Active Directory Domain Services. Ponieważ platforma Azure AD DS jest usługą zarządzaną, istnieją pewne zadania administracyjne, których nie można wykonać, na przykład przy użyciu protokołu RDP (Remote Desktop Protocol) do łączenia się z kontrolerami domeny.

W tym samouczku pokazano, jak utworzyć maszynę wirtualną z systemem Windows Server na platformie Azure i zainstalować wymagane narzędzia do administrowania domeną zarządzaną platformy Azure AD DS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Poznaj dostępne zadania administracyjne w domenie zarządzanej AD DS platformy Azure
> * Instalowanie Active Directory narzędzi administracyjnych na maszynie wirtualnej z systemem Windows Server
> * Używanie Centrum administracyjne usługi Active Directory do wykonywania typowych zadań

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna z systemem Windows Server dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby powyższy samouczek [tworzy maszynę wirtualną z systemem Windows Server i łączy ją z domeną zarządzaną][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz i skonfigurujesz maszynę wirtualną zarządzania przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Dostępne zadania administracyjne w usłudze Azure AD DS

Usługa Azure AD DS zapewnia domenę zarządzaną dla użytkowników, aplikacji i usług, które mają być używane. Takie podejście zmienia niektóre dostępne zadania zarządzania, które można wykonać, i jakie są uprawnienia w domenie zarządzanej. Te zadania i uprawnienia mogą być inne niż środowisko, w którym jest wykonywane regularne lokalne środowisko Active Directory Domain Services. Nie można też nawiązać połączenia z kontrolerami domeny w domenie zarządzanej AD DS platformy Azure przy użyciu Pulpit zdalny.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Zadania administracyjne, które można wykonywać w domenie zarządzanej AD DS platformy Azure

Członkowie grupy *Administratorzy domeny usługi AAD* mają uprawnienia do domeny zarządzanej AD DS platformy Azure, która umożliwia im wykonywanie zadań, takich jak:

* Przyłączanie maszyn do domeny zarządzanej.
* Skonfiguruj wbudowany obiekt zasad grupy dla *AADDC komputerów* i kontenerów *użytkowników AADDC* w domenie zarządzanej.
* Administrowanie systemem DNS w domenie zarządzanej.
* Utwórz niestandardowe jednostki organizacyjne (OU) w domenie zarządzanej i zarządzaj nimi.
* Uzyskiwanie dostępu administracyjnego do komputerów przyłączonych do domeny zarządzanej.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Uprawnienia administracyjne, których nie masz w domenie zarządzanej AD DS platformy Azure

Domena zarządzana AD DS platformy Azure jest zablokowana, dlatego nie masz uprawnień do wykonywania pewnych zadań administracyjnych w domenie. Niektóre z poniższych przykładów są zadaniami, których nie można wykonać:

* Rozwiń schemat domeny zarządzanej.
* Połącz się z kontrolerami domeny dla domeny zarządzanej przy użyciu Pulpit zdalny.
* Dodaj kontrolery domeny do domeny zarządzanej.
* Nie masz uprawnień *administratora domeny* ani *administratora przedsiębiorstwa* dla domeny zarządzanej.

## <a name="sign-in-to-the-windows-server-vm"></a>Logowanie do maszyny wirtualnej z systemem Windows Server

W poprzednim samouczku maszyna wirtualna z systemem Windows Server została utworzona i przyłączona do domeny zarządzanej AD DS platformy Azure. Użyjmy tej maszyny wirtualnej, aby zainstalować narzędzia do zarządzania. W razie potrzeby [wykonaj kroki opisane w samouczku, aby utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].

> [!NOTE]
> W tym samouczku użyjesz maszyny wirtualnej z systemem Windows Server na platformie Azure, która jest przyłączona do domeny zarządzanej AD DS platformy Azure. Można również użyć klienta systemu Windows, takiego jak Windows 10, który jest przyłączony do domeny zarządzanej.
>
> Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz [instalowanie narzędzia administracji zdalnej serwera (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Aby rozpocząć, Połącz się z maszyną wirtualną z systemem Windows Server w następujący sposób:

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie. Wybierz grupę zasobów, w której utworzono maszynę wirtualną, taką jak moja *zasobów*, a następnie wybierz maszynę wirtualną, taką jak *myVM*.
1. W oknach **przeglądowych** maszyny wirtualnej wybierz pozycję **Połącz**.

    ![Połącz się z maszyną wirtualną z systemem Windows w Azure Portal](./media/tutorial-create-management-vm/connect-vm.png)

    Możesz również [utworzyć i użyć hosta usługi Azure bastionu (obecnie w wersji zapoznawczej)][azure-bastion] , aby zezwolić na dostęp tylko za Azure Portal pośrednictwem protokołu SSL.

1. Wybierz opcję *pobrania pliku RDP*. Zapisz ten plik RDP w przeglądarce sieci Web.
1. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.
1. Wprowadź poświadczenia użytkownika, który jest częścią grupy *administratorów DC usługi Azure AD* , np. *contoso\dee*
1. Jeśli podczas procesu logowania zobaczysz ostrzeżenie o certyfikacie, wybierz pozycję **tak** lub **Kontynuuj** , aby nawiązać połączenie.

## <a name="install-active-directory-administrative-tools"></a>Instalowanie Active Directory narzędzi administracyjnych

Zarządzanie domenami zarządzanymi AD DS platformy Azure odbywa się przy użyciu tych samych narzędzi administracyjnych co lokalne środowiska AD DS, takich jak Centrum administracyjne usługi Active Directory (usługach ADAC) lub AD PowerShell. Narzędzia te można instalować w ramach funkcji Narzędzia administracji zdalnej serwera (RSAT) w systemie Windows Server i komputerach klienckich. Członkowie grupy *Administratorzy domeny usługi AAD* mogą następnie administrować zdalnie AD DS domenami zarządzanymi przy użyciu tych narzędzi administracyjnych usługi AD z komputera, który jest przyłączony do domeny zarządzanej.

Aby zainstalować narzędzia administracyjne Active Directory na maszynie wirtualnej przyłączonej do domeny, wykonaj następujące czynności:

1. **Menedżer serwera** powinna być otwarta domyślnie po zalogowaniu się do maszyny wirtualnej. Jeśli nie, w menu **Start** wybierz **Menedżer serwera**.
1. W okienku *pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
1. Na stronie **zanim rozpoczniesz** *Kreatora dodawania ról i funkcji*wybierz pozycję **dalej**.
1. W polu *Typ instalacji*pozostaw zaznaczoną opcję **Instalacja oparta na rolach lub** oparta na funkcjach, a następnie wybierz pozycję **dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, takiej jak *MyVM.contoso.com*, a następnie wybierz przycisk **dalej**.
1. Na stronie **role serwera** kliknij przycisk **dalej**.
1. Na stronie **funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera** , a następnie rozwiń węzeł Narzędzia do **administrowania rolami** .

    Wybierz funkcję **narzędzi AD DS i AD LDS** z listy narzędzi do administrowania rolami, a następnie wybierz przycisk **dalej**.

    ![Zainstaluj narzędzia "AD DS i AD LDS Tools" na stronie funkcje](./media/tutorial-create-management-vm/install-features.png)

1. Na stronie **potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi administracyjnych może potrwać minutę lub dwa.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij** , aby zakończyć działanie kreatora **dodawania ról i funkcji** .

## <a name="use-active-directory-administrative-tools"></a>Korzystanie Active Directory narzędzi administracyjnych

Po zainstalowaniu narzędzi administracyjnych Zobaczmy, jak używać ich do administrowania domeną zarządzaną platformy Azure AD DS. Upewnij się, że logujesz się do maszyny wirtualnej przy użyciu konta użytkownika, który jest członkiem grupy *Administratorzy domeny usługi AAD* .

1. Z menu **Start** wybierz pozycję **Narzędzia administracyjne systemu Windows**. Zostaną wyświetlone narzędzia administracyjne usługi AD zainstalowane w poprzednim kroku.

    ![Lista narzędzi administracyjnych zainstalowanych na serwerze](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Wybierz **Centrum administracyjne usługi Active Directory**.
1. Aby poznać domenę zarządzaną platformy Azure AD DS, w lewym okienku wybierz nazwę domeny, na przykład *contoso.com*. Dwa kontenery o nazwie *komputery AADDC* i *Użytkownicy AADDC* znajdują się u góry listy.

    ![Wyświetl listę dostępnych kontenerów należącej do domeny zarządzanej AD DS platformy Azure](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Aby wyświetlić użytkowników i grupy należące do domeny zarządzanej AD DS platformy Azure, wybierz kontener **AADDC Users (Użytkownicy** ). Konta użytkowników i grupy z dzierżawy usługi Azure AD są wymienione w tym kontenerze.

    W poniższych przykładowych danych wyjściowych konto użytkownika o nazwie *contosoadmin* i Grupa dla *administratorów usługi AAD* są wyświetlane w tym kontenerze.

    ![Wyświetl listę użytkowników domeny usługi Azure AD DS w Centrum administracyjne usługi Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Aby wyświetlić komputery, które są przyłączone do domeny zarządzanej AD DS platformy Azure, wybierz kontener **komputery AADDC** . Zostanie wyświetlona pozycja dla bieżącej maszyny wirtualnej, na przykład *myVM*. Konta komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej AD DS platformy Azure, są przechowywane w tym kontenerze *komputery AADDC* .

Typowe akcje Centrum administracyjne usługi Active Directory, takie jak resetowanie hasła konta użytkownika lub zarządzanie członkostwem w grupie, są dostępne. W celu zarządzania typowymi akcjami w domenie zarządzanej AD DS platformy Azure można również użyć *modułu Active Directory dla środowiska Windows PowerShell*, który jest instalowany jako część narzędzi administracyjnych.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Poznaj dostępne zadania administracyjne w domenie zarządzanej AD DS platformy Azure
> * Instalowanie Active Directory narzędzi administracyjnych na maszynie wirtualnej z systemem Windows Server
> * Używanie Centrum administracyjne usługi Active Directory do wykonywania typowych zadań

Aby bezpiecznie współdziałać z domeną zarządzaną platformy Azure AD DS, Włącz protokół Secure Lightweight Directory Access Protocol (LDAPs).

> [!div class="nextstepaction"]
> [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
