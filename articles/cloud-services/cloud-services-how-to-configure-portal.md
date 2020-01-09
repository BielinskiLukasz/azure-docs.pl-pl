---
title: Jak skonfigurować usługę w chmurze (Portal) | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługi w chmurze na platformie Azure. Dowiedz się, jak zaktualizować konfigurację usługi w chmurze i skonfigurować dostęp zdalny do wystąpień roli. W poniższych przykładach użyto Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361011"
---
# <a name="how-to-configure-cloud-services"></a>Jak skonfigurować Cloud Services

W Azure Portal można skonfigurować najczęściej używane ustawienia dla usługi w chmurze. Jeśli wolisz bezpośrednio aktualizować pliki konfiguracji, pobierz odpowiedni plik, a następnie przekaż jego zaktualizowaną wersję i zaktualizuj usługę w chmurze, wprowadzając zmiany w konfiguracji. W obu przypadkach aktualizacje konfiguracji są przekazywane do wszystkich wystąpień ról.

Do nich można także zarządzać wystąpieniami ról usługi w chmurze lub pulpitu zdalnego.

Platforma Azure może zapewnić dostępność usługi na 99,95% w trakcie aktualizacji konfiguracji, jeśli masz co najmniej dwa wystąpienia roli dla każdej roli. Dzięki temu jedna maszyna wirtualna może przetwarzać żądania klientów, gdy druga jest aktualizowana. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Zmień usługę w chmurze

Po otwarciu [Azure Portal](https://portal.azure.com/)przejdź do usługi w chmurze. W tym miejscu można zarządzać wieloma aspektami.

![Strona Ustawienia](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Link **Ustawienia** lub **wszystkie ustawienia** spowoduje otwarcie **ustawień** , w których można zmienić **Właściwości**, zmienić **konfigurację**, zarządzać **certyfikatami**, skonfigurować **reguły alertów**i zarządzać **użytkownikami** mającymi dostęp do tej usługi w chmurze.

![Ustawienia usługi w chmurze platformy Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Zarządzaj wersją systemu operacyjnego gościa

Domyślnie platforma Azure okresowo aktualizuje system operacyjny gościa do najnowszej obsługiwanego obrazu w rodzinie systemów operacyjnych określonym w konfiguracji usługi (. cscfg), takiej jak Windows Server 2016.

Jeśli musisz wybrać konkretną wersję systemu operacyjnego, możesz ustawić ją w **konfiguracji**.

![Ustaw wersję systemu operacyjnego](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Wybranie określonej wersji systemu operacyjnego spowoduje wyłączenie automatycznych aktualizacji systemu operacyjnego i naprawienie odpowiedzialności. Musisz się upewnić, że wystąpienia roli otrzymują aktualizacje lub że aplikacja może ujawnić luki w zabezpieczeniach.

## <a name="monitoring"></a>Monitorowanie

Możesz dodać alerty do usługi w chmurze. Kliknij pozycję **ustawienia** > **reguły alertów** > **Dodawanie alertu**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

W tym miejscu możesz skonfigurować alert. Za pomocą pola listy rozwijanej **Metryka** można skonfigurować alert dla następujących typów danych.

* Odczyt dysku
* Zapis na dysku
* Ruch przychodzący w sieci
* Ruch wychodzący w sieci
* Procent użycia procesora CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurowanie monitorowania na podstawie kafelka metryki

Zamiast korzystać z **ustawień** > **regułami alertów**można kliknąć jeden z kafelków metryk w sekcji **monitorowanie** w usłudze w chmurze.

![Monitorowanie usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

W tym miejscu możesz dostosować wykres używany z kafelkiem lub dodać regułę alertu.

## <a name="reboot-reimage-or-remote-desktop"></a>Ponowne uruchamianie, odtwarzanie obrazu lub Pulpit zdalny

Pulpit zdalny można skonfigurować za pomocą [Azure Portal (skonfigurować pulpit zdalny)](cloud-services-role-enable-remote-desktop-new-portal.md), [programu PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)lub za pomocą [programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Aby ponownie uruchomić, odtworzyć z obrazu lub zdalnie do usługi w chmurze, wybierz wystąpienie usługi w chmurze.

![Wystąpienie usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Następnie można zainicjować Podłączanie pulpitu zdalnego, zdalnie ponownie uruchomić wystąpienie lub zdalnie odtworzyć z obrazu (Zacznij od nowego obrazu) wystąpienia.

![Przyciski wystąpienia usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Skonfiguruj ponownie. cscfg

Może być konieczne ponowne skonfigurowanie usługi w chmurze za pomocą pliku [konfiguracji usługi (cscfg)](cloud-services-model-and-package.md#cscfg) . Najpierw musisz pobrać plik. cscfg, zmodyfikować go, a następnie przekazać.

1. Kliknij ikonę **Ustawienia** lub link **wszystkie ustawienia** , aby otworzyć **Ustawienia**.

    ![Strona Ustawienia](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kliknij element **konfiguracji** .

    ![Blok konfiguracji](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kliknij przycisk **Pobierz**.

    ![Pobierz](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po zaktualizowaniu pliku konfiguracji usługi należy przekazać i zastosować aktualizacje konfiguracji:

    ![Przekazywanie](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Wybierz plik. cscfg i kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [niestandardową nazwę domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).
* Skonfiguruj [Certyfikaty SSL](cloud-services-configure-ssl-certificate-portal.md).



