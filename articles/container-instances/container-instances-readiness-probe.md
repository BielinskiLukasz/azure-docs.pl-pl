---
title: Konfigurowanie sondy gotowości dla wystąpienia kontenera
description: Dowiedz się, jak skonfigurować sondę, aby upewnić się, że kontenery w Azure Container Instances odbierają żądania tylko wtedy, gdy są gotowe
ms.topic: article
ms.date: 10/17/2019
ms.openlocfilehash: 50cb341788434a6dc0bb0a1423d9e59a3d93634d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901855"
---
# <a name="configure-readiness-probes"></a>Konfigurowanie sond gotowości

W przypadku aplikacji kontenerowych, które obsługują ruch, warto sprawdzić, czy kontener jest gotowy do obsługi żądań przychodzących. Azure Container Instances obsługuje sondy gotowości do uwzględnienia konfiguracji, aby nie można było uzyskać dostępu do kontenera w pewnych warunkach. Sonda gotowości zachowuje się jak [sonda gotowości Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Na przykład aplikacja kontenera może wymagać załadowania dużego zestawu danych podczas uruchamiania i nie ma potrzeby otrzymywania żądań w tym czasie.

W tym artykule wyjaśniono, jak wdrożyć grupę kontenerów zawierającą sondę gotowości, aby kontener odbierał ruch tylko wtedy, gdy sonda się powiedzie.

Azure Container Instances obsługuje również [sondy na żywo](container-instances-liveness-probe.md), które można skonfigurować, aby spowodować automatyczne ponowne uruchomienie kontenera w złej kondycji.

> [!NOTE]
> Obecnie nie można używać sondy gotowości w grupie kontenerów wdrożonej w sieci wirtualnej.

## <a name="yaml-configuration"></a>Konfiguracja YAML

Przykładowo utwórz plik `readiness-probe.yaml` z następującym fragmentem kodu zawierającym sondę gotowości. Ten plik definiuje grupę kontenerów, która składa się z kontenera z uruchomioną małą aplikacją internetową. Aplikacja jest wdrażana z publicznego obrazu `mcr.microsoft.com/azuredocs/aci-helloworld`. Ta aplikacja kontenera jest również przedstawiona w przewodnikach Szybki Start, takich jak [Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure](container-instances-quickstart.md).

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Uruchom polecenie

Plik YAML zawiera polecenie uruchamiania, które ma zostać uruchomione podczas uruchamiania kontenera, zdefiniowane przez właściwość `command`, która akceptuje tablicę ciągów. To polecenie symuluje czas, gdy aplikacja sieci Web jest uruchomiona, ale kontener nie jest gotowy. Po pierwsze uruchamia sesję powłoki i uruchamia `node` polecenie w celu uruchomienia aplikacji sieci Web. Uruchamia również polecenie do uśpienia przez 240 sekund, po którym tworzony jest plik o nazwie `ready` w katalogu `/tmp`:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Gotowość — polecenie

Ten plik YAML definiuje `readinessProbe`, który obsługuje polecenie `exec` gotowości, które działa jako Kontrola gotowości. To przykładowe polecenie gotowości sprawdza, czy istnieje plik `ready` w katalogu `/tmp`.

Gdy plik `ready` nie istnieje, polecenie gotowości kończy działanie z wartością różną od zera. kontener kontynuuje działanie, ale nie można uzyskać do niego dostępu. Gdy polecenie zakończy się pomyślnie z kodem zakończenia 0, kontener jest gotowy do uzyskania dostępu. 

Właściwość `periodSeconds` określa, że polecenie gotowości powinno być wykonywane co 5 sekund. Sonda gotowości jest uruchamiana przez okres istnienia grupy kontenerów.

## <a name="example-deployment"></a>Przykładowe wdrożenie

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów z poprzednią konfiguracją YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Wyświetl kontrole gotowości

W tym przykładzie w ciągu pierwszych 240 sekund polecenie gotowości zakończy się niepowodzeniem, gdy sprawdza obecność pliku `ready`. Kod stanu zwrócił sygnały, że kontener nie jest gotowy.

Te zdarzenia można wyświetlić w Azure Portal lub interfejs wiersza polecenia platformy Azure. Na przykład w portalu są wyświetlane zdarzenia typu `Unhealthy` są wyzwalane w przypadku niepowodzenia polecenia gotowości. 

![Zdarzenie w złej kondycji portalu][portal-unhealthy]

## <a name="verify-container-readiness"></a>Weryfikuj gotowość kontenera

Po uruchomieniu kontenera można sprawdzić, czy nie jest on dostępny jako pierwszy. Po zainicjowaniu obsługi administracyjnej Pobierz adres IP grupy kontenerów:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Spróbuj uzyskać dostęp do lokacji, gdy sonda gotowości nie powiedzie się:

```bash
wget <ipAddress>
```

Wyjście pokazuje, że witryna nie jest dostępna początkowo:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Po 240 sekundach polecenie gotowości zakończy się pomyślnie, sygnalizując kontener jest gotowy. Teraz po uruchomieniu polecenia `wget` pomyślne:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Gdy kontener jest gotowy, możesz również uzyskać dostęp do aplikacji sieci Web, przechodząc do adresu IP przy użyciu przeglądarki sieci Web.

> [!NOTE]
> Sonda gotowości kontynuuje działanie przez okres istnienia grupy kontenerów. Jeśli polecenie gotowości nie powiedzie się w późniejszym czasie, kontener ponownie staną się niedostępne. 
> 

## <a name="next-steps"></a>Następne kroki

Sonda gotowości może być przydatna w scenariuszach obejmujących grupy wielokontenerowe, które składają się z kontenerów zależnych. Aby uzyskać więcej informacji o scenariuszach obejmujących wiele kontenerów, zobacz [grupy kontenerów w Azure Container Instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
