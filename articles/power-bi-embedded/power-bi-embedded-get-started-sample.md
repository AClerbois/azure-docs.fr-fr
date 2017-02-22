---
title: "Prise en main d’un exemple"
description: "Power BI Embedded, utiliser SDK pour ajouter des rapports interactifs Power BI à votre application business intelligence"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2f0f36e7ffeec52bacc35ac5039cd183976dc3aa
ms.openlocfilehash: 9c9ab36ac90c3e73a21c7e53a6715cc4503a8e99


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Prise en main de l’exemple Power BI Embedded
Avec **Microsoft Power BI Embedded**, vous pouvez intégrer des rapports Power BI dans vos applications web ou mobiles. Dans cet article, nous vous présenterons l’exemple de prise en main **Power BI Embedded** .

Avant de poursuivre, vous souhaiterez probablement enregistrer les ressources suivantes. Elles vous aideront lors de l’intégration de rapports Power BI dans l’exemple d’application et vos propres applications également.

* [Exemple d’application web de tableau de bord](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Référence de l’API Power BI Embedded](https://msdn.microsoft.com/library/mt711493.aspx)
* [Kit de développement logiciel (SDK) .NET Power BI Embedded ](http://go.microsoft.com/fwlink/?LinkId=746472) (disponible par le biais de NuGet)

> [!NOTE]
> Avant de configurer et d’exécuter l’exemple de prise en main de Power BI Embedded, vous devez créer au moins une **collection d’espaces de travail** dans votre abonnement Azure. Pour savoir comment créer une **collection d’espaces de travail** dans le portail Azure, consultez [Prise en main de Power BI Embedded (aperçu)](power-bi-embedded-get-started.md).
>
>

## <a name="configure-the-sample-app"></a>Configurer l’exemple d’application
Passons à la configuration de votre environnement de développement Visual Studio pour accéder aux composants nécessaires à l’exécution de l’exemple d’application.

1. Téléchargez et décompressez l’exemple [Power BI Embedded - Intégrer un rapport dans une application web](http://go.microsoft.com/fwlink/?LinkId=761493) sur GitHub.
2. Ouvrez **PowerBI-embedded.sln** dans Visual Studio. Vous devrez peut-être exécuter la commande **Update-Package** dans la Console du Gestionnaire de Package NuGet pour mettre à jour les packages utilisés dans cette solution.
3. Générez la solution.
4. Exécutez l’application de console **ProvisionSample** . Dans l’exemple d’application console, vous allez approvisionner un espace de travail et importer un fichier PBIX.
5. Pour approvisionner un nouvel **espace de travail**, sélectionnez l’option 5, **Approvisionner un nouvel espace de travail dans une collection d’espaces de travail existante**.

    ![](media/powerbi-embedded-get-started-sample/console-option-5.png)
6. Entrez le nom de votre **collection d’espaces de travail** et la **clé d’accès**. Vous pouvez les obtenir dans le **Portail Azure**. Pour en savoir plus sur la façon d’obtenir votre **clé d’accès**, consultez [Affichage des touches d’accès rapide aux API de Power BI](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) dans Prise en main de Microsoft Power BI Embedded.

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. Copiez et enregistrez **l’ID d’espace de travail** qui vient d’être créé et qui sera utilisé ultérieurement dans cet article. Une fois l’**ID d’espace de travail** créé, il est disponible dans le **portail Azure**.

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. Pour importer un fichier PBIX dans votre **espace de travail**, sélectionnez l’option **6. Importez le fichier PBIX Desktop dans un espace de travail existant**. Si vous n’avez pas de fichier PBIX sous la main, téléchargez [l’exemple PBIX Analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547).
9. Si vous y êtes invité, entrez un nom convivial pour votre **jeu de données**.

La réponse doit ressembler à ceci :

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
>
>

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
>
>

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
>
>

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)



<!--HONumber=Jan17_HO1-->


