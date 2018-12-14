---
title: Odkryj wbudowanej zapory wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić ochronę za pomocą wbudowanych zapory w wystąpieniu zarządzanym usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: d7b781a2cfcf17285b105a104588fe1569dffe4b
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346226"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego

Wystąpienie zarządzane [reguły zabezpieczeń ruchu przychodzącego obowiązkowe](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) wymaga portów zarządzania 9000, 9003, 1438, 1440, 1452 być otwarte z **dowolnego źródła** na grupy zabezpieczeń sieci (NSG), chroniące zarządzane Wystąpienie. Mimo że te porty zostały otwarte na poziomie sieciowych grup zabezpieczeń, są chronione na poziomie sieci przez zapory wbudowanych.

## <a name="verify-firewall"></a>Sprawdź zapory

Aby sprawdzić, czy te porty, należy użyć dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak użyć jednej z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wystąpienia zarządzane przez usługę i łączność, zobacz [architektura łączności usługi Azure SQL bazy danych zarządzane wystąpienia](sql-database-managed-instance-connectivity-architecture.md).