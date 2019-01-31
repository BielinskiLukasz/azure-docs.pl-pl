---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server | Microsoft Docs
description: Dowiedz się, jak połączyć i zaloguj się do maszyny Wirtualnej Windows przy użyciu witryny Azure portal i modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4bfb17a7c50e97ae71908f052f7f38110cf376df
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296891"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Jak połączyć i logowanie się na maszynie wirtualnej platformy Azure, systemem Windows
Korzystając z przycisku **Połącz** w witrynie Azure Portal, uruchomisz sesję pulpitu zdalnego z poziomu pulpitu systemu Windows. Najpierw nawiążesz połączenie z maszyną wirtualną, a następnie zaloguj się.

Aby połączyć się z maszyny Wirtualnej z systemem Windows z poziomu komputera Mac, konieczne będzie zainstalowanie klienta RDP dla komputerów Mac, takie jak [Pulpit zdalny Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **maszyn wirtualnych**.
3. Wybierz maszynę wirtualną z listy.
4. Górnej części strony dla maszyny wirtualnej, wybierz **Connect**.
2. Na **Połącz z maszyną wirtualną** wybierz odpowiedni adres IP i port. W większości przypadków domyślny adres IP i port powinny być używane. Wybierz **pliku RDP Pobierz**pliku RDP Pobierz **. Jeśli maszyna wirtualna ma zestaw zasad just-in-time, musisz najpierw wybrać **żądania dostępu** przycisk, aby zażądać dostępu, przed pobraniem pliku RDP. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [zarządzanie dostępem maszyny wirtualnej przy użyciu tylko w zasadach czasu](../../security-center/security-center-just-in-time.md).
2. Otwórz pobrany plik RDP i wybierz polecenie **Połącz**, gdy wyświetli się odpowiedni monit. 
2. Zostanie wyświetlone ostrzeżenie, że `.rdp` plik pochodzi od nieznanego wydawcy. Jest to oczekiwane. W **Podłączanie pulpitu zdalnego** wybierz **Connect** aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wprowadź poświadczenia dla konta na maszynie wirtualnej, a następnie wybierz pozycję **OK**.
   
     **Konto lokalne**: Jest to zazwyczaj konta lokalnego, nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny**: Jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domeny*&#92;*Username*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny**: Jeśli maszyna wirtualna jest kontrolerem domeny, wprowadź nazwę użytkownika i hasło konta administratora domeny dla tej domeny.
4. Wybierz **tak** do zweryfikowania tożsamości maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Jeśli **Connect** przycisk w portalu jest nieaktywny i nie są połączone na platformie Azure za pośrednictwem [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) połączenie, musisz utworzyć i Przypisz publiczny adres IP maszyny Wirtualnej przed użyciem pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [publiczne adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Łączenie z maszyną wirtualną przy użyciu programu PowerShell

Jeśli są przy użyciu programu PowerShell i mieć zainstalowany moduł AzureRM może nawiązywać połączenie przy użyciu `Get-AzureRmRemoteDesktopFile` polecenia cmdlet, jak pokazano poniżej.

W tym przykładzie zostanie uruchomiony natychmiast połączenia RDP, poruszają podobne monity jako powyżej.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Możesz również zapisać plik RDP do użytku w przyszłości.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli masz trudności z połączeniem, zobacz [połączeń pulpitu zdalnego Rozwiązywanie problemów z](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

