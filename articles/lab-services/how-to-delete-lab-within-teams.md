---
title: Usuwanie laboratorium Azure Lab Services z zespołów
description: Dowiedz się, jak usunąć laboratorium Azure Lab Services z zespołów.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 1d2fe73d33a88e595f42b47c1e7122dcbdfe1063
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094451"
---
# <a name="delete-labs-within-teams"></a>Usuwanie laboratoriów w zespołach

W tym artykule pokazano, jak usunąć laboratorium z aplikacji **Azure Lab Services** .

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz konto usługi laboratoryjnej](tutorial-setup-lab-account.md#create-a-lab-account) w Azure Portal.
* [Rozpocznij pracę i Utwórz laboratorium usług laboratoryjnych w ramach zespołów](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Usuwanie laboratoriów

Laboratorium utworzone w zespołach można usunąć w [witrynie sieci Web usług Lab Services](https://labs.azure.com) , usuwając laboratorium bezpośrednio, zgodnie z opisem w temacie [Zarządzanie laboratoriami klas w Azure Lab Services](how-to-manage-classroom-labs.md). 

Usuwanie laboratorium jest również wyzwalane, gdy zespół zostanie usunięty. W przypadku usunięcia zespołu, w którym laboratorium zostanie utworzone, laboratorium zostanie automatycznie usunięte po 24 godzinach od momentu wyzwolenia automatycznej synchronizacji listy użytkowników. 

> [!IMPORTANT]
> Usunięcie karty lub odinstalowanie aplikacji nie spowoduje usunięcia laboratorium. 

Jeśli karta zostanie usunięta, użytkownicy na liście członkostwa zespołu nadal będą mogli uzyskiwać dostęp do maszyn wirtualnych w [witrynie sieci Web usług laboratoryjnych](https://labs.azure.com) , chyba że usunięcie laboratorium zostanie jawnie wyzwolone przez usunięcie laboratorium w witrynie sieci Web lub usunięcie zespołu. 

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Zarządzanie listami użytkowników laboratorium w zespołach](how-to-manage-user-lists-within-teams.md)
- [Zarządzaj pulą maszyn wirtualnych laboratorium w zespołach](how-to-manage-vm-pool-within-teams.md)
- [Tworzenie harmonogramów laboratorium w zespołach i zarządzanie nimi](how-to-create-schedules-within-teams.md)
- [Dostęp do maszyny wirtualnej w programie Teams — widok ucznia](how-to-access-vm-for-students-within-teams.md)

