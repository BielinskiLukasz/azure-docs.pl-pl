---
title: Delegowanie poddomeny usługi Azure DNS
description: Dowiedz się, jak delegować przywoływana jest poddomena DNS platformy Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/22/2019
ms.author: victorh
ms.openlocfilehash: 87a80703c473245660a850645ca3fef21bbd80f6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452721"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegowanie poddomeny usługi Azure DNS

Aby delegować domenę podrzędną DNS, można użyć witryny Azure portal. Na przykład, jeśli jesteś właścicielem domeny contoso.com, należy delegować domenę podrzędną o nazwie *inżynierów* inną, oddzielną strefę, które mogą być zarządzane oddzielnie od strefy contoso.com.

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować przywoływana jest poddomena DNS platformy Azure, musisz delegować domenę publiczną do usługi Azure DNS. Zobacz [delegować domenę do usługi Azure DNS](./dns-delegate-domain-azure-dns.md) instrukcje dotyczące sposobu konfigurowania serwerów nazw dla celów delegacji. Gdy domeny jest delegowane do strefy DNS platformy Azure, możesz delegować Twojej domeny podrzędnej.

W przykładach w tym artykule używany w domenie contoso.com. Należy zastąpić własnej domeny, korzystając z tych procedur.

## <a name="create-a-zone-for-your-subdomain"></a>Utwórz strefę w Twojej domenie podrzędnej

Najpierw należy utworzyć strefę dla **inżynierów** poddomeny.

1. W witrynie Azure portal, wybierz **Utwórz zasób**.
2. W polu wyszukiwania wpisz **DNS**i wybierz **strefy DNS**.
3. Wybierz pozycję **Utwórz**.
4. W **tworzenie strefy DNS** okienko, typ **engineering.contoso.com** w **nazwa** pola tekstowego.
5. Wybierz grupę zasobów dla swojej strefy. Można użyć tej samej grupie zasobów co strefa nadrzędna rozdzielaniu podobne zasoby.
6. Kliknij pozycję **Utwórz**.
7. Po pomyślnym zakończeniu wdrożenia przejdź do nowej strefy.

## <a name="note-the-name-servers"></a>Należy pamiętać, serwery nazw

Następnie skopiuj cztery serwery nazw dla swojej domeny podrzędnej.

1. Na **inżynierów** strefa okienko, należy pamiętać, cztery serwery nazw dla strefy. Te serwery nazw będzie on potrzebny później.
2. Tworzenie **A** rekordu na potrzeby testowania. Na przykład można utworzyć **www** A rejestrowanie i skonfigurować go za pomocą **10.10.10.10** adresu IP.

## <a name="create-an-ns-record"></a>Tworzenie rekordów NS

Następnie utwórz rekord serwera (nazw NS) nazwa **inżynierów** strefy.

1. Przejdź do strefy dla domeny nadrzędnej.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. Na **Dodawanie zestawu rekordów** okienko, typ **inżynierów** w **nazwa** pola tekstowego.
4. Aby uzyskać **typu**, wybierz opcję **NS**.
5. W obszarze **serwera nazw**, wprowadź czterech serwerów nazw, które wcześniej zarejestrowany na **inżynierów** strefy.
6. Kliknij przycisk **OK**.

## <a name="test-the-delegation"></a>Test delegowania

Nslookup umożliwia testowanie delegowanie.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz polecenie `nslookup www.engineering.<your domain name>.`
3. Powinna pojawić się jako nieautorytatywny odpowiedzi, wyświetlanie adresu **10.10.10.10**.



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).