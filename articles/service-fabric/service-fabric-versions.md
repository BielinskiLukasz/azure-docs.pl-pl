---
title: Obsługiwane wersje klastra w usłudze Azure Service Fabric
description: Dowiedz się więcej o wersjach klastra w usłudze Azure Service Fabric, łącznie z linkiem do najnowszych wydań z blogu zespołu Service Fabric.
ms.topic: troubleshooting
ms.date: 03/02/2020
ms.openlocfilehash: 76a8137bbe42d8f693ac807f640a78fc4ec87c2e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370427"
---
# <a name="supported-service-fabric-versions"></a>Obsługiwane Service Fabric wersje

Upewnij się, że w klastrze jest zawsze uruchomiona obsługiwana wersja usługi Azure Service Fabric. Co najmniej 60 dni po opublikowaniu nowej wersji Service Fabric, obsługa poprzedniej wersji zostanie zakończona. Anonse nowych wydań znajdziesz na [blogu zespołu Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Zapoznaj się z następującymi dokumentami, aby uzyskać szczegółowe informacje o tym, jak utrzymać klaster z obsługiwaną wersją Service Fabric:

- [Uaktualnianie klastra usługi Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Uaktualnij wersję Service Fabric działającą w autonomicznym klastrze systemu Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli wymieniono wersje Service Fabric i ich daty końcowe pomocy technicznej.

| Service Fabric środowisko uruchomieniowe w klastrze | Można uaktualnić bezpośrednio z wersji klastra |Zgodna wersja zestawu SDK lub pakietu NuGet | Koniec wsparcia |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed 5.3.121 | 5.1.158.* |Mniejsze niż lub równe wersji 2,3 |20 stycznia 2017 |
| 5.3.* | 5.1.158.* |Mniejsze niż lub równe wersji 2,3 |24 lutego 2017 |
| 5.4.* | 5.1.158.* |Mniejsze niż lub równe wersji 2,4 |10 maja 2017       |
| 5.5.* | 5.4.164.* |Mniejsze niż lub równe wersji 2,5 |10 sierpnia 2017 r.    |
| 5.6.* | 5.4.164.* |Mniejsze niż lub równe wersji 2,6 |13 października 2017 r.   |
| 5.7.* | 5.4.164.* |Mniejsze niż lub równe wersji 2,7 |15 grudnia 2017  |
| 6.0.* | 5.6.205.* |Mniejsze niż lub równe wersji 2,8 |30 marca 2018     |
| 6.1.* | 5.7.221.* |Mniejsze niż lub równe wersji 3,0 |15 lipca 2018      |
| 6.2.* | 6.0.232.* |Mniejsze niż lub równe wersji 3,1 |26 października 2018   |
| 6.3.* | 6.1.480.* |Mniejsze niż lub równe wersji 3,2 |31 marca 2019  |
| 6.4.* | 6.2.301.* |Mniejsze niż lub równe wersji 3,3 |15 września 2019 |
| 6.5.* | 6.4.617.* |Mniejsze niż lub równe wersji 3,4 |1 maja 2020 |
| 7,0. * | 7.0.466.* |Mniejsze niż lub równe wersji 4,0 |Bieżąca wersja, dlatego bez daty zakończenia |
## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne obsługiwane przez obsługiwane wersje Service Fabric.

| System operacyjny | Najwcześniejsza obsługiwana wersja Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| Windows Server 1709 | 6.0 |
| System Windows Server w wersji 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Obsługiwane nazwy wersji

W poniższej tabeli wymieniono nazwy wersji Service Fabric i odpowiadające im numery wersji.

| Nazwa wersji | Numer wersji systemu Windows | Numer wersji systemu Linux |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Nie dotyczy |
| 5,3 CU1 | 5.3.204.9494 | Nie dotyczy |
| 5,3 ZASTOSUJESZ PAKIETU CU2 | 5.3.301.9590 | Nie dotyczy |
| 5,3 CU3 | 5.3.311.9590 | Nie dotyczy |
| 5,4 ZASTOSUJESZ PAKIETU CU2 | 5.4.164.9494 | Nie dotyczy |
| 5,5 CU1 | 5.5.216.0    | Nie dotyczy |
| 5,5 ZASTOSUJESZ PAKIETU CU2 | 5.5.219.0    | Nie dotyczy |
| 5,5 CU3 | 5.5.227.0    | Nie dotyczy |
| 5,5 CU4 | 5.5.232.0    | Nie dotyczy |
| 5,6 RTO | 5.6.204.9494 | Nie dotyczy |
| 5,6 ZASTOSUJESZ PAKIETU CU2 | 5.6.210.9494 | Nie dotyczy |
| 5,6 CU3 | 5.6.220.9494 | Nie dotyczy |
| 5,7 RTO | 5.7.198.9494 | Nie dotyczy |
| 5,7 CU4 | 5.7.221.9494 | Nie dotyczy |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 ZASTOSUJESZ PAKIETU CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 ZASTOSUJESZ PAKIETU CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Nie dotyczy |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 ZASTOSUJESZ PAKIETU CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 ZASTOSUJESZ PAKIETU CU2 | 6.4.622.9590 | Nie dotyczy |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Nie dotyczy |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Nie dotyczy |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 ZASTOSUJESZ PAKIETU CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 ZASTOSUJESZ PAKIETU CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
