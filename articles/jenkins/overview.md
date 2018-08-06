---
title: Omówienie serwera Jenkins i platformy Azure | Microsoft Docs
description: Hostuj serwer automatyzacji kompilacji i wdrażania Jenkins na platformie Azure i używaj zasobów obliczeniowych i magazynu platformy Azure w celu rozszerzania potoków ciągłej integracji i wdrażania (CI/CD).
ms.topic: overview
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/25/2018
ms.openlocfilehash: ecb4ea7aee005cb539910b2cb25f0b84de7ba510
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281559"
---
# <a name="azure-and-jenkins"></a>Platforma Azure i serwer Jenkins

[Jenkins](https://jenkins.io/) to popularny serwer automatyzacji typu open-source używany do konfigurowania ciągłej integracji i dostarczania (CI/CD) dla projektów dotyczących oprogramowania. Można hostować swoje wdrożenie narzędzia Jenkins na platformie Azure lub rozszerzyć istniejącą konfigurację narzędzia Jenkins za pomocą zasobów platformy Azure. Są również dostępne wtyczki serwera Jenkins, które umożliwiają upraszczanie ciągłej integracji/ciągłego wdrażania aplikacji na platformie Azure.

Ten artykuł stanowi wprowadzenie do zagadnień dotyczących używania platformy Azure z narzędziem Jenkins i zawiera szczegółowe informacje na temat podstawowych funkcji platformy Azure dostępnych dla użytkowników serwera Jenkins. Aby zacząć korzystać z własnego serwera Jenkins na platformie Azure, zapoznaj się z naszym [przewodnikiem Szybki start](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hostowanie serwerów Jenkins na platformie Azure

Hostowanie serwera Jenkins na platformie Azure pozwala na centralizowanie automatyzacji kompilacji i skalowanie wdrożenia w miarę wzrostu potrzeb projektów dotyczących oprogramowania. Narzędzie Jenkins można wdrożyć na platformie Azure za pomocą:
 
- [Szablonu rozwiązania Jenkins](install-jenkins-solution-template.md) dostępnego w witrynie Azure Marketplace.
- [Maszyn wirtualnych platformy Azure](/azure/virtual-machines/linux/overview). Zobacz nasz [samouczek](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd), aby utworzyć wystąpienie serwera Jenkins na maszynie wirtualnej.
- Aby uzyskać informacje na temat klastra Kubernetes działającego w usłudze [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), zobacz nasze [instrukcje](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Do monitorowania wdrożenia serwera Jenkins na platformie Azure i zarządzania nim służy usługa [Log Analytics](/azure/log-analytics/log-analytics-overview) i [interfejs wiersza polecenia platformy Azure](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skalowanie automatyzacji kompilacji na żądanie

Dodawanie agentów kompilacji do istniejącego wdrożenia serwera Jenkins umożliwia skalowanie pojemności kompilacji narzędzia Jenkins w miarę wzrostu liczby kompilacji oraz stopnia złożoności zadań i potoków. Tych agentów kompilacji można uruchamiać na maszynach wirtualnych platformy Azure przy użyciu [wtyczki agentów maszyn wirtualnych platformy Azure](jenkins-azure-vm-agents.md). Zobacz nasz [samouczek](/azure/jenkins/jenkins-azure-vm-agents), aby uzyskać więcej informacji.

Po skonfigurowaniu przy użyciu [jednostki usługi platformy Azure](/azure/azure-resource-manager/resource-group-overview) potoki i zadania serwera Jenkins mogą korzystać z tego poświadczenia w celu:

- Bezpiecznego przechowywania i archiwizowania artefaktów kompilacji w usłudze [Azure Storage](/azure/storage/common/storage-introduction) przy użyciu [wtyczki usługi Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Aby dowiedzieć się więcej, zapoznaj się z [instrukcjami dotyczącymi magazynu serwera Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution).
- Zarządzania zasobami platformy Azure i konfigurowania ich za pomocą [interfejsu wiersza polecenia platformy Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Wdrażanie swojego kodu w usługach platformy Azure

Wtyczki serwera Jenkins umożliwiają wdrażanie aplikacji na platformie Azure w ramach potoków ciągłej integracji/ciągłego wdrażania narzędzia Jenkins. Wdrażanie w usługach [usługi Azure App Service](/azure/app-service/) i [Azure Container Service](/azure/container-service/kubernetes/) pozwala na wdrażanie przejściowe, testowanie i wydawanie aktualizacji do aplikacji bez konieczności zarządzania podstawową infrastrukturą.

 Wtyczki są dostępne w przypadku wdrażania w następujących usługach i środowiskach:

- [Aplikacja internetowa platformy Azure w systemie Linux](/azure/app-service/containers/app-service-linux-intro). Aby rozpocząć pracę, zobacz [samouczek](java-deploy-webapp-tutorial.md).
- [Aplikacja internetowa platformy Azure](/azure/app-service/app-service-web-overview). Aby rozpocząć pracę, zobacz [instrukcje](deploy-Jenkins-app-service-plugin.md).