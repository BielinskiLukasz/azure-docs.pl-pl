---
title: Wdrażanie aplikacji kontenera przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Azure Service Fabric
description: W ramach tego samouczka nauczysz się konfigurować ciągłą integrację i ciągłe wdrażanie aplikacji kontenera usługi Azure Service Fabric za pomocą usługi Visual Studio Team Services (VSTS).
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/29/2018
ms.author: ryanwi,v-steg
ms.custom: mvc
ms.openlocfilehash: 5ec898465b550a136db71ee731be5943c7441dcd
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44382163"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Samouczek: wdrażanie aplikacji kontenera przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric

Niniejszy samouczek jest drugą częścią serii i zawiera opis sposobu konfiguracji ciągłej integracji i ciągłego wdrażania aplikacji kontenera usługi Azure Service Fabric za pomocą usługi Visual Studio Team Services.  Wymagana jest istniejąca aplikacja usługi Service Fabric. Na potrzeby tego samouczka za przykład posłużyła aplikacja utworzona w temacie [Samouczek: wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric](service-fabric-host-app-in-a-container.md).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji w usłudze Team Services
> * Tworzenie definicji wydania w usłudze Team Services
> * Automatyczne wdrażanie i uaktualnianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Mieć klaster na platformie Azure lub [utworzyć go za pomocą tego samouczka](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrożyć w nim konteneryzowaną aplikację](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Przygotowywanie profilu publikowania

Teraz po [wdrożeniu aplikacji kontenera](service-fabric-host-app-in-a-container.md) wszystko jest gotowe do skonfigurowania ciągłej integracji.  Najpierw przygotuj profil publikowania w aplikacji na potrzeby procesu wdrażania wykonywanego w ramach usługi Team Services.  Profil publikowania należy skonfigurować pod kątem klastra, który został wcześniej utworzony.  Uruchom program Visual Studio i otwórz istniejący projekt aplikacji usługi Service Fabric.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Opublikuj...**.

Wybierz profil docelowy w ramach projektu aplikacji na potrzeby przepływu pracy ciągłej integracji, na przykład chmurę.  Określ punkt końcowy połączenia klastra.  Zaznacz pole wyboru **Uaktualnij aplikację**, aby uaktualnić aplikację dla każdego wdrożenia w usłudze Team Services.  Kliknij hiperlink **Zapisz**, aby zapisać ustawienia w profilu publikowania, a następnie kliknij przycisk **Anuluj** w celu zamknięcia okna dialogowego.

![Profil wypychania][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Udostępnianie rozwiązania programu Visual Studio w nowym repozytorium Git w usłudze Team Services

Udostępnij pliki źródłowe aplikacji w projekcie zespołowym usługi Team Services, aby umożliwić generowanie kompilacji.

Utwórz nowe lokalne repozytorium Git dla projektu, wybierając pozycję **Dodaj do kontroli źródła** -> **Git** na pasku stanu w prawym dolnym rogu programu Visual Studio.

W widoku **Wypychanie** w programie **Team Explorer** wybierz przycisk **Opublikuj repozytorium Git** w obszarze **Wypychanie do usługi Visual Studio Team Services**.

![Wypychanie repozytorium Git][push-git-repo]

Sprawdź swój adres e-mail i z listy rozwijanej **Domena usługi Team Services** wybierz swoje konto. Wprowadź nazwę repozytorium i wybierz polecenie **Publikuj repozytorium**.

![Wypychanie repozytorium Git][publish-code]

Opublikowanie repozytorium powoduje utworzenie nowego projektu zespołowego na Twoim koncie o takiej samej nazwie jak lokalne repozytorium. Aby utworzyć repozytorium w istniejącym projekcie zespołowym, kliknij pozycję **Zaawansowane** obok nazwy **Repozytorium**, a następnie wybierz projekt zespołowy. Aby wyświetlić kod w Internecie, wybierz polecenie **Wyświetl w Internecie**.

## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurowanie ciągłego dostarczania za pomocą usługi VSTS

Definicja kompilacji usługi Team Services zawiera opis przepływu pracy, który składa się z zestawu kroków kompilacji wykonywanych sekwencyjnie. Utwórz definicję kompilacji, która spowoduje wygenerowanie pakietu aplikacji usługi Service Fabric i innych artefaktów, na potrzeby wdrożenia w klastrze usługi Service Fabric. Dowiedz się więcej na temat [definicji kompilacji usługi Team Services](https://www.visualstudio.com/docs/build/define/create). 

Definicja wydania usługi Team Services opisuje przepływ pracy, który wdraża pakiet aplikacji w klastrze. Jednoczesne użycie definicji kompilacji i definicji wydania powoduje wykonanie całego przepływu pracy, zaczynając od plików źródłowych, a kończąc na aplikacji uruchomionej w klastrze. Dowiedz się więcej na temat[definicji wydania](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) usługi Team Services.

### <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji

Otwórz przeglądarkę internetową i przejdź do nowego projektu zespołowego na stronie: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Wybierz kartę **Kompilacja i wydawanie**, wybierz pozycję **Kompilacje**, a następnie pozycję **Nowy potok**.

![Nowy potok][new-pipeline]

Wybierz pozycję **VSTS Git** jako źródło, projekt zespołowy **Voting**, repozytorium **Voting** i domyślną gałąź **master** lub ręcznie i zaplanowane kompilacje.  Następnie kliknij pozycję **Kontynuuj**.

W obszarze **Wybieranie szablonu** wybierz szablon **Aplikacja usługi Azure Service Fabric z obsługą platformy Docker**, a następnie kliknij przycisk **Zastosuj**.

![Wybieranie szablonu kompilacji][select-build-template]

W obszarze **Zadania** wybierz **Hostowany program VS2017** jako **Pulę agentów**.

![Wybieranie zadań][task-agent-pool]

Kliknij opcję **Tagowanie obrazów**.

W obszarze **Typ rejestru kontenerów** wybierz pozycję **Azure Container Registry**. Wybierz wartość pola **Subskrypcja platformy Azure**, a następnie kliknij opcję **Autoryzuj**. Wybierz wartość pola **Azure Container Registry**.

![Wybranie opcji Tagowanie obrazów platformy Docker][select-tag-images]

Kliknij opcję **Wypychanie obrazów**.

W obszarze **Typ rejestru kontenerów** wybierz pozycję **Azure Container Registry**. Wybierz wartość pola **Subskrypcja platformy Azure**, a następnie kliknij opcję **Autoryzuj**. Wybierz wartość pola **Azure Container Registry**.

![Wybranie opcji Wypychanie obrazów platformy Docker][select-push-images]

W obszarze **Wyzwalacze** włącz ciągłą integrację, zaznaczając pozycję **Włącz ciągłą integrację**. W obszarze **Filtry gałęzi** kliknij pozycję **+ Dodaj**, a wartość domyślna pozycji **Specyfikacja gałęzi** zostanie ustawiona na **master**.

W oknie dialogowym **Zapisywanie potoku i kolejki kompilacji** kliknij pozycję **Zapisz i umieść w kolejce**, aby ręcznie uruchomić kompilację.

![Wybieranie wyzwalaczy][save-and-queue]

Kompilacje można również wyzwalać w ramach procesu wypychania lub ewidencjonowania. Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje**.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, określ definicję wydania, która wdraża aplikację w klastrze.

### <a name="create-a-release-definition"></a>Tworzenie definicji wydania

Wybierz kartę **Kompilacja i wydawanie**, wybierz pozycję **Wydania**, a następnie wybierz pozycję **+ Nowy potok**.  W obszarze **Wybieranie szablonu** wybierz z listy szablon **Wdrożenie usługi Azure Service Fabric**, a następnie przycisk **Zastosuj**.

![Wybieranie szablonu wydania][select-release-template]

Wybierz pozycję **Zadania**, **Środowisko 1**, a następnie pozycję **+Nowe**, aby dodać nowe połączenie klastra.

![Dodawanie połączenia klastra][add-cluster-connection]

W widoku **Dodawanie nowego połączenia z usługą Service Fabric** wybierz uwierzytelnianie **Na podstawie certyfikatu** lub **Azure Active Directory**.  Określ nazwę połączenia dla klastra „mysftestcluster” i następujący punkt końcowy klastra „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000” (lub punkt końcowy klastra, w którym przeprowadzane jest wdrożenie).

W przypadku uwierzytelniania opartego na certyfikatach dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera, którego użyto do utworzenia klastra.  W obszarze **Certyfikat klienta** dodaj plik certyfikatu klienta zakodowany przy użyciu kodowania base-64. Zobacz pomoc podręczną dla tego pola, aby uzyskać informacje dotyczące sposobu uzyskania reprezentacji certyfikatu w kodowaniu base-64. Dodaj również **Hasło** certyfikatu.  Jeśli nie masz osobnego certyfikatu klienta, możesz użyć certyfikatu klastra lub serwera.

W przypadku poświadczeń usługi Azure Active Directory dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera użytego do utworzenia klastra oraz poświadczenia w polach **Nazwa użytkownika** i **Hasło**, których chcesz użyć w celu nawiązania połączenia z klastrem.

Kliknij przycisk **Dodaj**, aby zapisać połączenie klastra.



W obszarze Faza agenta, kliknij opcję **Wdrażanie aplikacji usługi Service Fabric**.
Kliknij przycisk **Ustawienia platformy Docker**, a następnie opcję **Konfiguruj ustawienia platformy Docker**. W obszarze **Źródło poświadczeń rejestru** wybierz opcję **Połączenie usługi Azure Resource Manager**. Następnie wybierz swoją **subskrypcję platformy Azure**.

![Agent potoku wydawania][release-pipeline-agent]

Następnie dodaj artefakt kompilacji do potoku, aby umożliwić definicji wydania odnalezienie danych wyjściowych kompilacji. Wybierz pozycję **Potok**, a następnie **Artefakty**->**+ Dodaj**.  W obszarze **Źródło (definicja kompilacji)** wybierz wcześniej utworzoną definicję kompilacji.  Kliknij przycisk **Dodaj**, aby zapisać artefakt kompilacji.

![Dodawanie artefaktu][add-artifact]

Włącz wyzwalacz ciągłego wdrażania, aby umożliwić automatyczne tworzenie wydania po zakończeniu kompilacji. Kliknij ikonę pioruna w artefakcie, włącz wyzwalacz, a następnie kliknij przycisk **Zapisz** w celu zapisania definicji wydania.

![Włączanie wyzwalacza][enable-trigger]

Wybierz pozycję **+ Wydanie** -> **Tworzenie wydania** -> **Utwórz**, aby ręcznie utworzyć wydanie. Postęp wydawania możesz monitorować na karcie **Wydania**.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę i przejdź pod adres [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170616.3”.

## <a name="commit-and-push-changes-trigger-a-release"></a>Zatwierdzanie i wypychanie zmian, wyzwalanie wydania

Aby upewnić się, że potok ciągłej integracji działa prawidłowo, zaewidencjonuj jakieś zmiany kodu w usłudze Team Services.

Podczas pisania kodu zmiany są automatycznie śledzone przez program Visual Studio. Zatwierdź zmiany w lokalnym repozytorium Git, wybierając ikonę oczekujących zmian (![Oczekujące][pending]) na pasku stanu w prawym dolnym rogu.

W widoku **Zmiany** programu Team Explorer dodaj komunikat opisujący aktualizację i zatwierdź wprowadzone zmiany.

![Zatwierdź wszystko][changes]

Wybierz ikonę paska stanu nieopublikowanych zmian (![Nieopublikowane zmiany][unpublished-changes]) lub widok synchronizacji w programie Team Explorer. Wybierz pozycję **Wypchnij**, aby zaktualizować kod w usłudze Team Services lub na serwerze TFS.

![Wypychanie zmian][push]

Wypychanie zmian do usługi Team Services automatycznie wyzwala kompilację.  Po pomyślnym zakończeniu definicji kompilacji automatycznie tworzone jest wydanie, które rozpoczyna uaktualnianie aplikacji w klastrze.

Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje** modułu **Team Explorer** w programie Visual Studio.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, określ definicję wydania, która wdraża aplikację w klastrze.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę i przejdź pod adres [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170815.3”.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Wprowadź zmiany w kodzie aplikacji.  Zapisz i zatwierdź zmiany, postępując zgodnie z poprzednimi krokami.

Po rozpoczęciu uaktualnienia aplikacji możesz obserwować jego postęp w narzędziu Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Uaktualnianie aplikacji może potrwać kilka minut. Po zakończeniu uaktualniania aplikacja zostanie uruchomiona w następnej wersji.  W tym przykładzie „1.0.0.20170815.4”.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji
> * Tworzenie definicji wydania
> * Automatyczne wdrażanie i uaktualnianie aplikacji

W następnej części tego samouczka dowiesz się, jak skonfigurować [monitorowanie kontenera](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
