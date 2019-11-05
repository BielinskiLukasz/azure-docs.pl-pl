---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Dowiedz się więcej o logice przetwarzania reguł zapory platformy Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518205"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall

Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.

## <a name="network-rules-and-applications-rules"></a>Reguły sieci i zasady dotyczące aplikacji

Reguły dotyczące sieci są stosowane najpierw, a następnie reguły aplikacji. Reguły są przerywane. Dlatego jeśli w regułach sieciowych zostanie znalezione dopasowanie, reguły aplikacji nie są przetwarzane.  Jeśli nie zostanie dopasowana reguła sieci i jeśli protokół pakietu to HTTP/HTTPS, pakiet zostanie oceniony przez reguły aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet jest oceniany względem kolekcji reguł infrastruktury. Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="nat-rules"></a>Reguły translatora adresów sieciowych

Połączenie przychodzące można włączyć przez skonfigurowanie translacji docelowego adresu sieciowego (DNAT) zgodnie z opisem w [samouczku: filtrowanie ruchu przychodzącego za pomocą usługi Azure firewall DNAT przy użyciu Azure Portal](../firewall/tutorial-firewall-dnat.md). Reguły DNAT są stosowane jako pierwsze. Jeśli zostanie znalezione dopasowanie, niejawna odpowiadająca reguła sieci umożliwia dodanie przetłumaczonego ruchu. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Dla tych połączeń nie są stosowane żadne reguły aplikacji.

## <a name="inherited-rules"></a>Dziedziczone reguły

Kolekcje reguł sieciowych dziedziczone z zasad nadrzędnych są zawsze priorytetowe względem kolekcji reguł sieci, które są zdefiniowane w ramach nowych zasad. Ta sama logika ma zastosowanie również do kolekcji reguł aplikacji. Jednak kolekcje reguł sieci są zawsze przetwarzane przed kolekcjami reguł aplikacji niezależnie od dziedziczenia.

Domyślnie zasady dziedziczą tryb analizy zagrożeń zasad nadrzędnych. Można to zastąpić, ustawiając tryb analizy zagrożeń na inną wartość na stronie Ustawienia zasad. Można przesłonić tylko wartość bardziej rygorystyczną. Na przykład jeśli zasady nadrzędne są ustawione tylko na *alerty*, można skonfigurować te zasady lokalne w taki sposób, aby były *wyzwalane i odrzucane*, ale nie można ich wyłączyć.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat wersji zapoznawczej Menedżera zapory platformy Azure](overview.md)