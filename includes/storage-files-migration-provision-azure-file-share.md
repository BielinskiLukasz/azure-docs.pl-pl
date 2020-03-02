---
title: Zagadnienia dotyczące aprowizacji udziałów plików platformy Azure.
description: Udostępnianie udziałów plików platformy Azure do użycia z Azure File Sync. Wspólny blok tekstu współużytkowany przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209430"
---
Udział plików platformy Azure jest przechowywany w chmurze na koncie usługi Azure Storage.
Poniżej znajdują się inne zagadnienia dotyczące wydajności.

Jeśli masz wysoce aktywne udziały — udziały używane przez wielu użytkowników i/lub aplikacje, wówczas dwa udziały plików platformy Azure mogą osiągnąć limit wydajności konta magazynu.

Najlepszym rozwiązaniem jest wdrożenie kont magazynu z jednym udziałem plików.
Można umieścić wiele udziałów plików platformy Azure w ramach tego samego konta magazynu, na przykład w przypadku udziałów archiwalnych lub w przypadku niewielkich codziennych działań.

Te zagadnienia dotyczą tylko bezpośredniego dostępu do chmury (za pośrednictwem maszyny wirtualnej platformy Azure) niż w przypadku Azure File Sync. Jeśli planujesz używać tylko Azure File Sync w tych udziałach, to zgrupowanie kilku na jedno konto usługi Azure Storage jest bardzo precyzyjne.

Jeśli utworzono listę udziałów, należy zmapować każdy udział na konto magazynu, w którym się znajdują.

W poprzedniej fazie określono odpowiednią liczbę udziałów. W tym kroku utworzono mapowanie kont magazynu do udziałów plików. Wdróż teraz odpowiednią liczbę kont usługi Azure Storage z odpowiednią liczbą udziałów plików platformy Azure.

Upewnij się, że region każdego z kont magazynu jest taki sam i jest zgodny z regionem zasobu usługi synchronizacji magazynu, który został już wdrożony.

> [!CAUTION]
> Jeśli utworzysz 100 TiB limit udziałów plików platformy Azure, udział ten może używać tylko lokalnie nadmiarowy magazyn lub strefy nadmiarowej nadmiarowości magazynu. Przed użyciem udziałów plików 100 TiB należy wziąć pod uwagę potrzeby nadmiarowości magazynu.

Udziały plików platformy Azure są nadal tworzone domyślnie z limitem 5 TiB. Ponieważ tworzysz nowe konta magazynu, upewnij się, że postępuj zgodnie ze [wskazówkami, aby utworzyć konta magazynu, które zezwalają na udziały plików platformy Azure z limitem 100 TIB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Innym zagadnieniem podczas wdrażania konta magazynu jest nadmiarowość usługi Azure Storage. Zobacz: [Opcje nadmiarowości usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

Nazwy zasobów są również ważne. Na przykład Jeśli grupujesz wiele udziałów dla działu kadr na koncie usługi Azure Storage, musisz odpowiednio nazwać konto magazynu. Podobnie podczas nadawania nazw udziałom plików platformy Azure należy używać nazw podobnych do tych, które są używane na potrzeby swoich lokalnych odpowiedników.