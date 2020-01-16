---
title: Koszty oprogramowania dla rezerwacji platformy Azure
description: Dowiedz się, które mierniki użytkowania oprogramowania nie są uwzględnione w kosztach wystąpienia usługi Azure Reserved VM Instances.
author: yashar
manager: yashar
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2019
ms.author: banders
ms.openlocfilehash: 7807129061328d1c6d563b9058987388e84c6e4c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995691"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Nieuwzględnianie kosztów oprogramowania w przypadku usługi Azure Reserved VM Instances

Rabaty na wystąpienia zarezerwowane maszyny wirtualnej i wydajność rezerwową programu SQL mają zastosowanie tylko do kosztów infrastruktury, a nie do kosztów oprogramowania. Jeśli używasz maszyny wirtualnej z systemem Windows i nie masz korzyści użycia hybrydowego platformy Azure w wystąpieniach zarezerwowanych maszyn wirtualnych, w przypadku mierników oprogramowania wymienionych w poniższej sekcji są naliczane opłaty. Jeśli nie wybrano korzyści użycia hybrydowego platformy Azure, w przypadku wdrożeń SQL PaaS opłata za używanie adresu IP nadal będzie naliczana przy użyciu oddzielnego miernika.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Mierniki oprogramowania systemu Windows nieuwzględnione w koszcie rezerwacji

| MeterId | Nazwa miernika w pliku użycia | Używane przez maszynę wirtualną |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Zarezerwowane maszyny z systemem Windows Server — seria (1-rdzeniowe) | Seria B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Zarezerwowane maszyny z systemem Windows Server — seria (2-rdzeniowe) | Seria B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Zarezerwowane maszyny z systemem Windows Server — seria (4-rdzeniowe) | Seria B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Zarezerwowane maszyny z systemem Windows Server — seria (8-rdzeniowe) | Seria B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Zarezerwowane maszyny z systemem Windows Server (1-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Zarezerwowane maszyny z systemem Windows Server (2-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Zarezerwowane maszyny z systemem Windows Server (4-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Zarezerwowane maszyny z systemem Windows Server (6-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Zarezerwowane maszyny z systemem Windows Server (8-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Zarezerwowane maszyny z systemem Windows Server (12-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Zarezerwowane maszyny z systemem Windows Server (16-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Zarezerwowane maszyny z systemem Windows Server (20-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Zarezerwowane maszyny z systemem Windows Server (24-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Zarezerwowane maszyny z systemem Windows Server (32-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Zarezerwowane maszyny z systemem Windows Server (40-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Zarezerwowane maszyny z systemem Windows Server (64-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Zarezerwowane maszyny z systemem Windows Server (72-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Zarezerwowane maszyny z systemem Windows Server (128-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Zarezerwowane maszyny z systemem Windows Server (256-rdzeniowe) | Wszystkie z wyjątkiem serii B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Zarezerwowane maszyny z systemem Windows Server (96-rdzeniowe) | Wszystkie z wyjątkiem serii B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Mierniki oprogramowania usług w chmurze nieuwzględnione w koszcie rezerwacji

| MeterId | Nazwa miernika w pliku użycia |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Licencja na usługi Cloud Services — 1 procesor wirtualny|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Licencja na usługi Cloud Services — 2 procesory wirtualne|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Licencja na usługi Cloud Services — 4 procesory wirtualne|
|13103090-ca72-4825-ab12-7f16c4931d95|Licencja na usługi Cloud Services — 8 procesorów wirtualnych|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Licencja na usługi Cloud Services — 16 procesorów wirtualnych|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Licencja na usługi Cloud Services — 20 procesorów wirtualnych|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Licencja na usługi Cloud Services — 32 procesory wirtualne|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Licencja na usługi Cloud Services — 64 procesory wirtualne|
|7a803026-244c-4659-834c-11e6b2d6b76f|Licencja na usługi Cloud Services — 80 procesorów wirtualnych|

## <a name="get-rates-for-azure-meters"></a>Uzyskaj stawki za mierniki platformy Azure

Informacje o koszcie poszczególnych mierników można uzyskać za pomocą interfejsu API usługi Azure RateCard. Aby dowiedzieć się, jak uzyskać stawki miernika platformy Azure, zobacz [Pobieranie informacji o cenach i metadanych dotyczących zasobów używanych w ramach subskrypcji platformy Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md)
- [Omówienie stosowania rabatu na rezerwacje](../manage/understand-vm-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
