---
title: Szybki start — tworzenie rejestru w portalu
description: Szybko dowiedz się, aby utworzyć prywatny rejestr platformy Docker w usłudze Azure Container Registry za pomocą witryny Azure portal.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409282"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Szybki start: tworzenie prywatnego rejestru kontenerów przy użyciu portalu Azure

Rejestr kontenerów platformy Azure to prywatny rejestr platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów platformy Docker i zarządzać nimi. W tym przewodniku Szybki start utworzysz rejestr kontenerów za pomocą witryny Azure Portal. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

Aby zalogować się do rejestru i pracować z obrazami kontenerów w tym przewodniku Szybki start, musisz korzystać z interfejsu wiersza polecenia platformy Azure (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Musisz mieć również zainstalowane lokalnie środowisko Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Wybierz pozycję Utwórz rejestr > **kontenerów kontenerów** >  **kontenerów kontenerów****kontenerów**.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-01]

Na karcie **Podstawy** wprowadź wartości **dla grupy zasobów** i nazwy **rejestru**. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Na potrzeby tego przewodnika Szybki start utwórz w lokalizacji `West US` nową grupę zasobów o nazwie `myResourceGroup`, a dla opcji **SKU** wybierz wartość „Podstawowa”. 

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-03]

Zaakceptuj wartości domyślne dla pozostałych ustawień. Następnie wybierz **pozycję Recenzja + utwórz**. Po zapoznaniu się z ustawieniami wybierz pozycję **Utwórz**.

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług, zobacz [Jednostki SKU rejestru kontenerów][container-registry-skus].

Kiedy pojawi się komunikat **Wdrażanie zakończyło się pomyślnie**, wybierz rejestr kontenerów w portalu. 

![Omówienie rejestru kontenerów w witrynie Azure Portal][qs-portal-05]

Zanotuj wartość **Serwer logowania**. Ta wartość jest używana w poniższych krokach podczas wypychania i ściągania obrazów za pomocą platformy Docker.

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do wystąpienia usługi ACR. Otwórz powłokę poleceń w systemie operacyjnym i użyj polecenia [az acr login][az-acr-login] w interfejsie wiersza polecenia platformy Azure. (Określ tylko nazwę rejestru podczas logowania. Nie dołączaj sufiksu "azurecr.io".

```azurecli
az acr login --name <acrName>
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Aby wyświetlić listę obrazów w rejestrze, przejdź do rejestru w portalu i wybierz pozycję **Repozytoria**, a następnie wybierz repozytorium utworzone za pomocą polecenia `docker push`.

W tym przykładzie wybieramy repozytorium **hello-world** i `v1`widzimy obraz -tagged w obszarze **Tagi**.

![Wyświetlanie listy obrazów kontenerów w witrynie Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby, przejdź do grupy zasobów **myResourceGroup** w portalu. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** w celu usunięcia grupy zasobów, rejestru kontenerów i przechowywanych w nim obrazów kontenerów.

![Usuwanie grupy zasobów w witrynie Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w witrynie Azure Portal, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
