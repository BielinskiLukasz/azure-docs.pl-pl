---
title: Połącz z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc
description: Połącz z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939196"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Połącz z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc

W tym artykule wyjaśniono, jak można nawiązać połączenie z wystąpieniem zarządzanym SQL z włączonym usługą Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Wyświetlanie wystąpień zarządzanych SQL z obsługą usługi Azure Arc

Aby wyświetlić wystąpienie zarządzane SQL z włączonym użyciem usługi Azure Arc i zewnętrzne punkty końcowe, użyj następującego polecenia:

```console
azdata arc sql mi list
```

Dane wyjściowe powinny wyglądać następująco:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Jeśli używasz AKS lub kubeadm lub OpenShift itp., możesz skopiować zewnętrzny adres IP i numer portu z tego miejsca i połączyć się z nim przy użyciu ulubionego narzędzia do łączenia się z wystąpieniem programu SQL Server/Azure SQL, takim jak Azure Data Studio lub SQL Server Management Studio.  Jeśli jednak korzystasz z maszyny wirtualnej szybkiego startu, zobacz poniżej, aby uzyskać szczegółowe informacje na temat łączenia się z tą maszyną wirtualną poza platformą Azure. 

> [!NOTE]
> Zasady firmowe mogą blokować dostęp do adresu IP i portu, zwłaszcza jeśli zostanie on utworzony w chmurze publicznej.

## <a name="connect"></a>Connect 

Nawiązywanie połączenia z Azure Data Studio, SQL Server Management Studio lub SQLCMD

Otwórz Azure Data Studio i nawiąż połączenie z wystąpieniem z zewnętrznym adresem IP i numerem portu punktu końcowego. W przypadku korzystania z maszyny wirtualnej platformy Azure potrzebny jest _publiczny_ adres IP, który jest identyfikowany za pomocą [specjalnej uwagi na temat wdrożeń maszyn wirtualnych platformy Azure](#special-note-about-azure-virtual-machine-deployments).

Przykład:

- Serwer: 52.229.9.30, 30913
- Nazwa użytkownika: sa
- Hasło: określone hasło SQL w czasie aprowizacji

> [!NOTE]
> Możesz użyć Azure Data Studio [wyświetlić pulpity nawigacyjne wystąpienia zarządzanego SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Aby nawiązać połączenie przy użyciu narzędzia SQLCMD lub Linux lub Windows, można użyć polecenia w taki sposób. Wprowadź hasło SQL po wyświetleniu monitu:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Specjalna Uwaga dotycząca wdrożeń maszyn wirtualnych platformy Azure

W przypadku korzystania z maszyny wirtualnej platformy Azure adres IP punktu końcowego nie będzie zawierał publicznego adresu IP. Aby zlokalizować zewnętrzny adres IP, użyj następującego polecenia:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Można następnie połączyć publiczny adres IP z portem, aby nawiązać połączenie.

Może być również konieczne uwidocznienie portu wystąpienia programu SQL Server za pomocą bramy zabezpieczeń sieci (sieciowej grupy zabezpieczeń). Aby zezwolić na ruch za pośrednictwem (sieciowej grupy zabezpieczeń), musisz dodać regułę, którą można wykonać przy użyciu poniższego polecenia.

Aby ustawić regułę, należy znać nazwę sieciowej grupy zabezpieczeń, którą można sprawdzić przy użyciu poniższego polecenia:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Po utworzeniu nazwy sieciowej grupy zabezpieczeń można dodać regułę zapory za pomocą następującego polecenia. Przykładowe wartości w tym miejscu tworzą regułę sieciowej grupy zabezpieczeń dla portu 30913 i umożliwia połączenie z **dowolnego** źródłowego adresu IP.  Nie jest to najlepsze rozwiązanie w zakresie zabezpieczeń.  Można zablokować lepsze działania, określając wartość-Source-Address-prefix, która jest specyficzna dla adresu IP klienta lub zakresu adresów IP, który obejmuje adresy IP Twojego zespołu lub organizacji.

Zastąp wartość `--destination-port-ranges` parametru poniżej numerem portu uzyskanym z `azdata sql instance list` powyższego polecenia F.

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie pulpitów nawigacyjnych wystąpienia zarządzanego SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Wyświetlanie wystąpienia zarządzanego SQL w Azure Portal](view-arc-data-services-inventory-in-azure-portal.md)
