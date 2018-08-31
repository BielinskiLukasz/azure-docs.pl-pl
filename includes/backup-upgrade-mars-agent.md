---
title: Uaktualnienie agenta usługi Azure Backup
description: Te informacje wyjaśnia, dlaczego należy uaktualnić agenta usługi Azure Backup i skąd pobrać uaktualnienia.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 2a969240da6e16f5a5ba76605a6efa2a96f9e4dd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285638"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnij agenta usług MARS

Wersje agenta usługi Microsoft Azure Recovery Service (MARS) poniżej 2.0.9083.0 są zależne od Azure Access Control service (ACS). Agenta usług MARS jest również określany jako agent usługi Kopia zapasowa Azure. W 2018 r. Azure [przestarzałe Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Począwszy od 19 marca 2018 r., wszystkie wersje agenta usług MARS poniżej 2.0.9083.0 doświadczy niepowodzeniami tworzenia kopii zapasowych. Aby uniknąć lub rozwiązać niepowodzeniami tworzenia kopii zapasowych [uaktualniania agenta usług MARS do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=229525). Aby zidentyfikować serwery wymagające uaktualnienia agenta MARS, wykonaj kroki opisane w [blogu kopii zapasowej dla uaktualnienie agentów MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agent usług MARS jest używany do tworzenia kopii zapasowych plików i folderów oraz dane o stanie systemu Azure. System Center DPM i usługi Azure Backup Server umożliwia agenta usług MARS utworzyć kopię zapasową danych na platformie Azure.
