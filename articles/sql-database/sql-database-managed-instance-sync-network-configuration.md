---
title: Usługa Azure App Service — synchronizacja konfiguracji sieci | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób synchronizacji konfiguracji sieci dla planu hostingu usługi Azure App Service.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 02c64fcd51862d8ff9f107adb0ca5cfda5812a88
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567115"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizacja konfiguracji sieci dla planu hostingu usługi Azure App Service

Może się zdarzyć, że chociaż możesz [zintegrować aplikację z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), nie można ustanowić połączenia z wystąpieniem zarządzanym. Jest jedyną operacją, której możesz spróbować odświeżyć konfiguracji sieci dla planu usługi. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizacja konfiguracji sieci dla usługi App Service plan hostingu

W tym celu wykonaj następujące kroki:  

1. Przejdź do aplikacji sieci web planu usługi App Service.
 
   ![plan usługi app service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kliknij przycisk **sieć** a następnie kliknij przycisk **kliknij tutaj, aby zarządzanie**.
 
   ![plan usługi zarządzania](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Wybierz swoje **sieci wirtualnej** i kliknij przycisk **Synchronizuj sieć**. 
 
   ![Synchronizuj sieć](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Zaczekaj, aż synchronizacja jest wykonywane.
  
   ![Synchronizacja gotowe](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teraz można przystąpić do spróbuj ponownie nawiązać połączenie do wystąpienia zarządzanego.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej do wystąpienia zarządzanego, zobacz [architektury zarządzanych sieci wirtualnej wystąpienia](sql-database-managed-instance-connectivity-architecture.md) i [Konfigurowanie istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md).
