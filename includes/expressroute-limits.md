---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fc63dc53d61a8b2e26f97cc09a359b3f2c7665c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901787"
---
| Zasób | Domyślny/maksymalny limit |
| --- | --- |
| Obwody usługi ExpressRoute na subskrypcję |10 |
| Obwody usługi ExpressRoute na region na subskrypcję z Azure Resource Manager |10 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure przy użyciu standardu ExpressRoute |4,000 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure przy użyciu dodatku ExpressRoute Premium |10 000 |
| Maksymalna liczba tras anonsowanych z prywatnej komunikacji równorzędnej Azure z przestrzeni adresowej sieci wirtualnej dla połączenia ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych do komunikacji równorzędnej firmy Microsoft przy użyciu standardu ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych w komunikacji równorzędnej firmy Microsoft z dodatkiem ExpressRoute Premium |200 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w tej samej lokalizacji komunikacji równorzędnej |4 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w różnych lokalizacjach komunikacji równorzędnej |4 |
| Liczba dozwolonych linków sieci wirtualnej na obwód ExpressRoute |Zobacz [liczbę sieci wirtualnych na tabelę obwodu ExpressRoute](#vnetpercircuit) .  |

#### <a name="vnetpercircuit"></a>Liczba sieci wirtualnych na obwód ExpressRoute
| **Rozmiar obwodu** | **Liczba linków sieci wirtualnej dla warstwy Standardowa** | **Liczba linków sieci wirtualnej z dodatkiem Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

**100 GB/s ExpressRoute tylko bezpośrednie*

> [!NOTE]
> Liczba połączeń Global Reach jest ograniczona względem limitu połączeń sieci wirtualnej na obwód ExpressRoute. Na przykład obwód w warstwie Premium 10 GB/s zezwoli na 5 Global Reach połączeń i 95 połączeń z bramami usługi ExpressRoute lub 95 Global Reach i 5 połączeń z bramami ExpressRoute lub dowolną inną kombinację do limitu 100 połączeń dla obwodu.
