---
title: Wprowadzenie do żądań HTTP połączeń hybrydowych usługi Azure Relay w środowisku Node | Microsoft Docs
description: Napisz aplikację konsolową Node.js do obsługi żądań HTTP połączeń hybrydowych usługi Azure Relay w środowisku Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: e54a096bd27efddaa9eafb8619e787178550a6e0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759259"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Wprowadzenie do żądań HTTP połączeń hybrydowych usługi Relay w środowisku Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

W tym przewodniku Szybki start utworzysz aplikacje nadawcy i odbiorcy w środowisku Node.js umożliwiające wysyłanie i odbieranie komunikatów przy użyciu protokołu HTTP. Aplikacje korzystają z funkcji połączeń hybrydowych usługi Azure Relay. Aby uzyskać więcej ogólnych informacji o usłudze Azure Relay, zobacz [Azure Relay](relay-what-is-it.md). 

W tym przewodniku Szybki start wykonasz następujące kroki:

1. Utworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal.
2. Utworzenie połączenia hybrydowego w tej przestrzeni nazw za pomocą witryny Azure Portal.
3. Napisanie aplikacji konsolowej serwera (odbiornika) służącej do odbierania komunikatów.
4. Napisanie aplikacji konsolowej klienta (nadawcy) służącej do wysyłania komunikatów.
5. Uruchamianie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
- Środowisko [Node.js](https://nodejs.org/en/).
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-namespace-using-the-azure-portal"></a>Tworzenie przestrzeni nazw za pomocą usługi Azure Portal
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Tworzenie połączenia hybrydowego za pomocą witryny Azure Portal
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Tworzenie aplikacji serwera (odbiornika)
Aby nasłuchiwać i odbierać komunikaty z usługi Relay, napisz aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Tworzenie aplikacji klienta (nadawcy)

Aby wysyłać komunikaty do usługi Relay, możesz używać dowolnego klienta HTTP lub napisać aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Uruchamianie aplikacji

1. Uruchom aplikację serwera: w wierszu polecenia Node.js wpisz `node listener.js`.
2. Uruchom aplikację klienta: w wiersza polecenia Node.js wpisz `node sender.js`, a następnie wpisz dowolny tekst.
3. Upewnij się, że w konsoli aplikacji serwera pojawia się tekst wprowadzony w aplikacji klienta.

Gratulacje! Aplikacja typu end-to-end do obsługi połączeń hybrydowych korzystająca ze środowiska Node.js jest gotowa.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki start utworzono aplikacje klienta i serwera w środowisku Node.js służące do wysyłania i odbierania komunikatów za pomocą protokołu HTTP. Funkcja połączeń hybrydowych usługi Azure Relay obsługuje również wysyłanie i odbieranie komunikatów przy użyciu obiektów WebSocket. Aby dowiedzieć się, jak używać obiektów WebSocket z funkcją połączeń hybrydowych usługi Azure Relay, zobacz [Przewodnik Szybki start dotyczący obiektów WebSocket](relay-hybrid-connections-node-get-started.md).

W tym przewodniku Szybki start za pomocą środowiska Node.js utworzono aplikacje klienta i serwera. Aby dowiedzieć się, jak pisać aplikacje klienta i serwera na platformie .NET Framework, zobacz [Przewodnik Szybki start dotyczący obiektów WebSocket na platformie .NET](relay-hybrid-connections-dotnet-get-started.md) lub [Przewodnik Szybki start dotyczący protokołu HTTP na platformie .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).
