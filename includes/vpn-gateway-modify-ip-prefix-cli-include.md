---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b381c5ad8fd81cd9b7411e1f4679b3f5214e6de9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652863"
---
### <a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

Jeśli nie masz połączenia bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz użyć tego samego polecenia, za pomocą którego utworzono bramę sieci lokalnej: [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway). To polecenie umożliwia również zaktualizowanie adresu IP bramy dla urządzenia sieci VPN. W celu zastąpienia bieżących ustawień użyj istniejącej nazwy bramy sieci lokalnej. Jeśli użyjesz innej nazwy, utworzysz nową bramę sieci lokalnej, a nie zastąpisz istniejącej.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. Określ tylko prefiksy, które chcesz zachować. W tym przypadku są to prefiksy 10.0.0.0/24 i 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli masz połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz zaktualizować prefiksy przy użyciu polecenia [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway). Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. W tym przykładzie już istnieją prefiksy 10.0.0.0/24 i 20.0.0.0/24. Dodano prefiksy 30.0.0.0/24 i 40.0.0.0/24 oraz określono wszystkie 4 prefiksy podczas aktualizowania.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
