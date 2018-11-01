---
title: Omówienie tagi nazwy FQDN dla zapory usługi Azure
description: Dowiedz się więcej o FQDN znaczniki zapory usługi Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/1/2018
ms.author: victorh
ms.openlocfilehash: 897ea3856516b5429ffb770164f863d71e7ae0dd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419014"
---
# <a name="fqdn-tags-overview"></a>Omówienie tagi nazwy FQDN

FQDN tag reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzony z dobrze znanych usług firmy Microsoft. Tag nazwy FQDN w regułach aplikacji służy do zezwalania wymagane wychodzącego ruchu sieciowego przez zaporę.

Na przykład aby ręcznie zezwolić na Windows Update ruchu sieciowego przez zaporę, należy utworzyć wiele reguł aplikacji na dokumentację firmy Microsoft. Za pomocą tagów w pełni kwalifikowaną nazwę domeny, można utworzyć regułę aplikacji, obejmują **aktualizacji Windows** tagów, a teraz ruch sieciowy do punktów końcowych może przepływać za pośrednictwem zapory usługi Microsoft Windows Update.

Nie można utworzyć własne tagi w pełni kwalifikowaną nazwę domeny ani nie możesz określić które nazw FQDN, które są uwzględniane w tagu. Firma Microsoft zarządza nazw FQDN obejmowanymi przez tag nazwy FQDN i aktualizuje tag jako zmiany nazwy FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

W poniższej tabeli przedstawiono bieżące znaczniki nazwy FQDN, których można użyć. Firma Microsoft udostępnia te znaczniki i można oczekiwać, że dodatkowe tagi do dodania okresowo.

|Nazwa FQDN tagu  |Opis  |
|---------|---------|
|Windows Update     |Zezwalaj na dostęp ruchu wychodzącego do usługi Microsoft Update zgodnie z opisem w [sposobu konfigurowania zapory dla aktualizacji oprogramowania](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostyka systemu Windows|Zezwalaj na dostęp ruchu wychodzącego do wszystkich [punktów końcowych diagnostyki Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Usługa Microsoft Active Protection Service (MAPS)|Zezwalaj na dostęp ruchu wychodzącego do [mapy](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Środowisko App Service Environment (ASE)|Umożliwia dostęp ruchu wychodzącego do środowiska ASE platformy ruchu. Ten tag nie obejmuje klienta SQL i Storage punkty końcowe utworzone przez środowisko ASE. Powinno być włączone za pomocą [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodać ręcznie.<br><br>Aby uzyskać więcej informacji na temat integracji Azure zapory przy użyciu środowiska ASE, zobacz [blokowania środowiska App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Umożliwia dostęp ruchu wychodzącego do usługi Azure Backup.

> [!NOTE]
> Po wybraniu tagu w pełni kwalifikowaną nazwę domeny w regule aplikacji, w polu protokołu: port musi być równa **https**.

## <a name="next-steps"></a>Kolejne kroki

Informacje na temat wdrażania zapory platformy Azure, zobacz [samouczek: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md).