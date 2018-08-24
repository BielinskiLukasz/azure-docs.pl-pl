## <a name="rest"></a>Wdróż plik ZIP z interfejsami API REST 

Możesz użyć [usługa wdrażania interfejsów API REST](https://github.com/projectkudu/kudu/wiki/REST-API) można wdrożyć pliku zip do aplikacji na platformie Azure. Aby wdrożyć, wysłanie żądania POST do https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Żądanie POST musi zawierać plik zip w treści komunikatu. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. Aby uzyskać więcej informacji, zobacz [odwołanie do wdrożenia wypychania zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

W przypadku uwierzytelniania HTTP BASIC potrzebne są Twoje poświadczenia wdrożenia usługi App Service. Aby zobaczyć, jak ustawić poświadczenia wdrażania, zobacz [zestawu i zresetować poświadczenia na poziomie użytkownika](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Za pomocą programu cURL

W poniższym przykładzie użyto narzędzia cURL, aby wdrożyć plik zip. Zastąp symbole zastępcze `<username>`, `<password>`, `<zip_file_path>`, i `<app_name>`. Po wyświetleniu monitu przez program cURL wpisz hasło.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

To żądanie wyzwala wdrażanie powiadomień push z przesłanego pliku zip. Możesz przejrzeć bieżącej i wcześniejszych wdrożeń przy użyciu `https://<app_name>.scm.azurewebsites.net/api/deployments` punktu końcowego, jak pokazano w poniższym przykładzie narzędzie cURL. Ponownie `<app_name>` nazwą swojej aplikacji i `<deployment_user>` przy użyciu nazwy użytkownika poświadczeń wdrażania.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie użyto [Invoke RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) można wysłać żądania, który zawiera plik zip. Zastąp symbole zastępcze `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, i `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

To żądanie wyzwala wdrażanie powiadomień push z przesłanego pliku zip. Aby zapoznać się z bieżącej i wcześniejszych wdrożeń, uruchom następujące polecenia. Ponownie `<app_name>` symbol zastępczy.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
