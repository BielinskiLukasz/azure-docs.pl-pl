---
title: plik dołączania
description: plik dołączania
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c99bfd9fb69beb7b5b7088ebea84082d744b12e9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88031814"
---
## <a name="run-image-from-registry"></a>Uruchamianie obrazu z rejestru

Teraz można ściągnąć i uruchomić obraz kontenera `hello-world:v1` z rejestru kontenerów za pomocą polecenia [docker run][docker-run]:

```
docker run <login-server>/hello-world:v1  
```

Przykładowe dane wyjściowe: 

```
Unable to find image 'mycontainerregistry.azurecr.io/hello-world:v1' locally
v1: Pulling from hello-world
Digest: sha256:662dd8e65ef7ccf13f417962c2f77567d3b132f12c95909de6c85ac3c326a345
Status: Downloaded newer image for mycontainerregistry.azurecr.io/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

[...]
```

<!-- LINKS - External -->
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
