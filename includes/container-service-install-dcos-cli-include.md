---
title: Instalowanie interfejsu wiersza polecenia DC/OS | Microsoft Docs
description: Instalowanie interfejsu wiersza polecenia DC/OS.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Kontenery, mikrousługi, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: a8ea47f158c0d666340815d2e039995c7483257f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23761968"
---
> [!NOTE]
> Te informacje dotyczą pracy z klastrami usługi ACS opartymi na kontrolerach domeny/systemach operacyjnych. Nie trzeba wykonywać tych czynności w przypadku klastrów usługi ACS opartych na aplikacji Swarm.
> 
> 

Najpierw [nawiąż połączenie z klastrem usługi ACS opartym na kontrolerze domeny/systemie operacyjnym](../articles/container-service/container-service-connect.md). Następnie możesz zainstalować interfejs wiersza polecenia DC/OS na komputerze klienckim przy użyciu poniższych poleceń:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Jeśli używasz starszej wersji środowiska Python, mogą zostać wyświetlone ostrzeżenia „InsecurePlatformWarnings”. Możesz je bezpiecznie zignorować.

Aby rozpocząć pracę bez ponownego uruchamiania powłoki, uruchom polecenie:

```bash
source ~/.bashrc
```

Ta czynność nie będzie konieczna w przypadku uruchomienia nowych powłok.

Teraz można potwierdzić, że interfejs wiersza polecenia został zainstalowany:

```bash
dcos --help
```