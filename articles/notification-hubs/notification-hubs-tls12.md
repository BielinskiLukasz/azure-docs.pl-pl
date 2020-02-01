---
title: Notification Hubs aktualizacje protokołu TLS
description: Dowiedz się więcej o obsłudze protokołu TLS w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908529"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Aby zapewnić wyższy poziom zabezpieczeń, Notification Hubs spowoduje wyłączenie obsługi protokołu TLS w wersji 1,0 i 1,1 30 kwietnia 2020. Te starsze protokoły zapewniają słabą kryptografię i są narażone na ataki BEAST i POODLE. Ta zmiana nie ma wpływu na aplikacje wdrożone na urządzeniach z systemem Android w wersji 5 lub nowszej lub iOS w wersji 5 lub nowszej, ponieważ te systemy operacyjne obsługują protokół TLS 1,2, a klient i serwer będą negocjować największą obsługiwaną przez siebie wersję Protokół po nawiązaniu połączenia.

Zalecamy zapoznanie się ze wszystkimi aplikacjami korzystającymi z usługi Azure Notification Hubs, aby upewnić się, że korzystają z najbardziej odpowiednich bibliotek i stosów protokołu TLS, które obsługują protokół TLS 1,2.

## <a name="update-apps"></a>Aktualizowanie aplikacji

Możesz upewnić się, że aplikacje dla systemu iOS używają protokołu TLS 1,2 przy użyciu funkcji zabezpieczeń sieci firmy Apple o nazwie App Transport Security (ATS). ATS nie można używać w przypadku zestawów SDK starszych niż iOS 9,0 lub macOS 10,11. Możesz również zapoznać się z [dokumentacją firmy Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

W przypadku aplikacji systemu Android korzystających z wystąpień SSLSocket należy ustawić włączone protokoły dla każdego wystąpienia SSLSocket, jak to zostało zanotowane w [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabela na stronie obsługa [zgodności protokołu TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ułatwia zamapowanie systemów operacyjnych ze zgodnymi wersjami protokołu TLS.

Aby uzyskać więcej informacji, zobacz Omówienie [obsługi protokołów TLS w systemie Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Następne kroki

- [Przegląd Notification Hubs](notification-hubs-push-notification-overview.md)