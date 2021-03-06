---
title: Omówienie zabezpieczeń usługi Azure Data Share
description: Omówienie zabezpieczeń usługi Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578097"
---
# <a name="security-overview-for-azure-data-share"></a>Omówienie zabezpieczeń usługi Azure Data Share

Ten artykuł zawiera omówienie zabezpieczeń usługi udziału danych platformy Azure.

## <a name="security-overview"></a>Omówienie zabezpieczeń

Udział danych platformy Azure wykorzystuje podstawowe zabezpieczenia oferowane przez platformę Azure w celu ochrony danych przechowywanych i przesyłanych. Dane są szyfrowane w spoczynku, gdzie są obsługiwane przez podstawowy magazyn danych. Dane są również szyfrowane podczas przesyłania przy użyciu protokołu TLS 1,2. Metadane dotyczące udziału danych są również szyfrowane w czasie spoczynku i podczas przesyłania. Udział danych platformy Azure nie przechowuje zawartości udostępnionych danych klienta.

Udział danych platformy Azure korzysta z tożsamości zarządzanej (znanej wcześniej jako MSI) w celu uzyskiwania dostępu do magazynów danych używanych do udostępniania danych. Nie ma wymiany poświadczeń między dostawcą danych i konsumentem danych. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [Zarządzanie tożsamościami dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Aby uzyskać więcej informacji na temat ról i uprawnień wymaganych do udostępniania danych, zapoznaj się z tematem [role i wymagania](concepts-roles-permissions.md).

Kontrola dostępu do udziału danych platformy Azure można ustawić na poziomie zasobów udostępniania danych, aby upewnić się, że jest on dostępny dla autoryzowanych. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Udostępnianie danych z lub do magazynów danych z włączoną zaporą
Aby udostępnić dane z lub do kont magazynu z włączoną zaporą, należy włączyć opcję **Zezwalaj na zaufane usługi firmy Microsoft** na koncie magazynu. Aby uzyskać szczegółowe informacje [, zobacz Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
