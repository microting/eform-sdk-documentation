---
title: API Reference

language_tabs:
  - csharp: C#
  - delphi: Delphi

toc_footers:
  - <a href='https://www.microting.com/kontakt/'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Documentation for Microting eForm .Net SDK

This is still a work in progress and subject to changes and thereby not a complete descriptions of what the SDK is capable of.
# Basic startup

## Setup the db

> To set up a the db for the first time, use this code:

```csharp
using  eFormCore;

public void setupDb() {
    string connectionStr = "some_connection_string_to_db";
    string token = "token";
    AdminTools adminTools = null;
    try
    {
         adminTools = new AdminTools(connectionStr);
    } catch
    {
        adminTools = new AdminTools(connectionStr);
    }
    
    adminTools.DbSetup(token);
}

```

```delphi
uses
  System.SysUtils, AdminTools;

procedure SetupDb;
var
  connectionStr: String;
  token: String;
  adminTools: TAdminTools;
begin
   connectionStr := 'some_connection_string_to_db';
   token := 'token';
   adminTools := nil;
   try
     adminTools := TAdminTools.Create(connectionStr);
   except
    on E: Exception do
       adminTools := TAdminTools.Create(connectionStr);
   end;
   adminTools.DbSetup(token);
end;
```


> Make sure to replace `token` with your API token.
> Make sure to replace `some_connection_string_to_db` with your MS SQL connection string.

Microting eForm uses API token keys to allow access to the API

<aside class="notice">
You must replace <code>token</code> with your personal API key.
</aside>

<aside class="notice">
You must replace <code>some_connection_string_to_db</code> with your MS SQL connection string.
</aside>

## Setup path for images and PDF documents

By default the SDK is using the below paths for pictures/signatures and PDF documents. Change these values in the Settings table as needed.

name | value
--------- | -------
fileLocationPicture | dataFolder/picture/ 
fileLocationPdf | dataFolder/pdf/

<aside class="notice">
Remeber the trialing / at the end of the path.
</aside> 
 
<aside class="warning">
DO NOT MODIFY ANY OTHER VALUE IN THE SETTINGS TABLE!
<br>As this will break the functionallity of the communication with Microting endpoints!
<br>All other variables will be updated automatically by the SDK.
</aside>

## Start the core for web usage

> To set up a basic core for web usage, use this code:

```cs
using eFormCore;

public Core getCore()
{
    string connectionStr = "some_connection_string_to_db";

    this.core = new Core();

    if (core.StartSqlOnly(connectionStr))
    {
        return core;
    }
    else
    {
        throw new Exception("Core is not running");
    }

}
```

```delphi
uses  System.SysUtils, Core;

function GetCore: TCore;
var
  connectionStr: String;
  core: TCore;
begin
  connectionStr := 'some_connection_string_to_db';

  core := TCore.Create;

  if (not core.StartSqlOnly(connectionStr)) then
  begin
    raise Exception.Create('Core is not running');
  end;


  result := core;
end;
```


> Make sure to replace `some_connection_string_to_db` with your MS SQL connection string.

Microting eForm uses API token keys to allow access to the API

<aside class="notice">
You must replace <code>token</code> with your personal API key.
</aside>

## Start the core for windows service or console

> To set up a basic core for windows service or console usage, use this code:

```cs
using eFormCore;

public Core getCore()
{
    string connectionStr = "some_connection_string_to_db";

    this.core = new Core();

    if (core.Start(connectionStr))
    {
        return core;
    }
    else
    {
        throw new Exception("Core is not running");
    }

}
```

```delphi
uses System.SysUtils, Core;

function GetCore: TCore;
var
  connectionStr: String;
  core: TCore;
begin
  connectionStr := 'some_connection_string_to_db';

  core := TCore.Create;

  if (not core.Start(connectionStr)) then
  begin
    raise Exception.Create('Core is not running');
  end;

  result := core;
end;
```


> Make sure to replace `some_connection_string_to_db` with your MS SQL connection string.

Microting eForm uses API token keys to allow access to the API

<aside class="notice">
You must replace <code>token</code> with your personal API key.
</aside>

# Local eForm templates

## Create a basic eForm template from XML

```cs
using eFormCore;
using eFormData;

public void SaveTemplate()
{
    Core core = getCore();
    
    MainElement newTemplate = core.TemplateFromXml(templateXmlString);
    newTemplate = core.TemplateUploadData(newTemplate);
    
    if (core.TemplateValidation(newTemplate).count == 0)
    {
        core.TemplateCreate(newTemplate);
    }
}

```

```delphi
uses System.SysUtils, Core, MainElement;

procedure SaveTemplate;
var
  core: TCore;
  newTemplate: TMainElement;
  templateXmlString: String;
begin
  core := GetCore;

  templateXmlString := 'some_template_xml_string';

  newTemplate := core.TemplatFromXml(templateXmlString);
  newTemplate := core.TemplateUploadData(newTemplate);

  if (core.TemplateValidation(newTemplate).Count = 0) then
  begin
     core.TemplateCreate(newTemplate);
  end;

end;

```

## Create a basic eForm template by objects

## Listing all eForm templates

```cs
using eFormCore;
using eFormShared;

public List<Template_Dto> ListTemplates()
{
    Core core = getCore();
    
    return core.TemplateItemReadAll(false);
}

```

```delphi
uses System.SysUtils, Generics.Collections, Core, Classes;

function ListTemplates: TObjectList<TTemplate_Dto>;
var
  core: TCore;
begin
  core := GetCore;
  
  result := core.TemplateItemReadAll(false);
end;
```

## Deleting an eForm template

```cs
using eFormCore;

public bool DeleteTemplate(int id)
{
    Core core = getCore();
    
    if (core.TemplateDelete(id))
        return true;
    else
        return false;
}
```

```delphi
uses System.SysUtils, Core;

function DeleteTemplate(id: integer): boolean;
var
  core: TCore;
begin
  core := GetCore;
  
  if (core.TemplateDelete(id)) then
    result := true
  else
    result := false;
end;
```


# Deployment of eForms

## Deploying to one site

```cs
using eFormCore;
using eFormData;

public List<string> DeployTo(int templateId, int siteId)
{
    Core core = getCore();  
    
    MainElement mainElement = core.TemplateRead(templateId);
    mainElement.Repeated = 0; 
    // We set this right now hardcoded, 
    // this will let the eForm be deployed until end date or we actively retract it.
    mainElement.EndDate = DateTime.Now.AddYears(10);
    mainElement.StartDate = DateTime.Now;
    
    SiteName_Dto site = core.Advanded_SiteItemRead(siteId);
    
    return core.CaseCreate(mainElement, "", site.SiteUId, "");
     
}
```

```delphi
uses System.SysUtils, System.Classes, System.DateUtils,
  Generics.Collections, Core, MainElement, Classes;

function DeployTo(templateId: integer; siteId: integer): TStringList;
var
  core: TCore;
  mainElement: TMainElement;
  site: TSiteName_Dto;
  siteIds: TList<Integer>;
begin
  core := GetCore;

  mainElement := core.TemplateRead(templateId);
  mainElement.Repeated := 0;
  // We set this right now hardcoded,
  // this will let the eForm be deployed until end date or we actively retract it.
  mainElement.StartDate := Date;
  mainElement.EndDate := IncYear(Date, 10);

  site := core.Advanced_SiteItemRead(siteId);

  siteIds := TList<Integer>.Create;
  siteIds.add(site.SiteUId);

  result := core.CaseCreate(mainElement, '', siteIds, '');
end;
```

## Deploying to one or more sites

```cs
using eFormCore;
using eFormData;

public List<string> DeployTo(int templateId, List<int> sitesToBeDeployedTo)
{
    Core core = getCore();
       
    MainElement mainElement = core.TemplateRead(templateId);
    mainElement.Repeated = 0; 
    // We set this right now hardcoded, 
    // this will let the eForm be deployed until end date or we actively retract it.
    mainElement.EndDate = DateTime.Now.AddYears(10);
    mainElement.StartDate = DateTime.Now;
    return core.CaseCreate(mainElement, "", sitesToBeDeployedTo, "");
}
```

```delphi
uses System.SysUtils, System.Classes, System.DateUtils,
  Generics.Collections, Core, MainElement;

function DeployTo(templateId: Integer; sitesToBeDeployedTo: TList<Integer>): TStringList;
var
  core: TCore;
  mainElement: TMainElement;
  siteIds: TList<Integer>;
begin
  core := GetCore;

  mainElement := core.TemplateRead(templateId);
  mainElement.Repeated := 0;
  // We set this right now hardcoded,
  // this will let the eForm be deployed until end date or we actively retract it.
  mainElement.StartDate := Date;
  mainElement.EndDate := IncYear(Date, 10);

  result := core.CaseCreate(mainElement, '', sitesToBeDeployedTo, '');
end;
```

> `sitesToBeDeployedTo` should contain the SiteUId for the sites to deploy to.


## Retracting deployments

```cs
using eFormCore;

public void DeployTo(int templateId, List<int> sitesToBeRetractedFrom)
{
    Core core = getCore();
    
    foreach (int siteUId in sitesToBeRetractedFrom)
    {
        core.CaseDelete(templateId, siteUId);
    }
}
```     

```delphi
uses System.SysUtils, System.Classes, Generics.Collections, Core;

procedure DeployTo(templateId: integer; sitesToBeRetractedFrom: TList<integer>);
var
  core: TCore;
  siteUID: integer;
begin
  core := GetCore;
  
  for siteUID in sitesToBeRetractedFrom do
  begin
     core.CaseDelete(templateId, siteUId);
  end;
end;
```
                                          
> `sitesToBeRetractedFrom` should contain the SiteUId for the sites to retract from.

## Listing deployed sites pr template

```cs
using eFormCore;

public List<SiteName_Dto> ListDeployedSites(int templateId)
{
    Core core = getCore();

    Template_Dto templateDto = core.TemplateItemRead(templateId);
    
    return templateDto.DeployedSites;    
}
```

```delphi
uses System.SysUtils, System.Classes, Generics.Collections,
  Core, Classes;

function ListDeployedSites(templateId: integer) : TObjectList<TSiteName_Dto>;
var
  core: TCore;
  templateDto: TTemplate_Dto;
begin
  core := GetCore;

  templateDto := core.TemplateItemRead(templateId);

  result := templateDto.DeployedSites;
end;
```

## Setting display index after deployment

```cs
using eFormCore;             
using eFormShared;

public void SetDisplayIndexForTemplate(int templateId, int displayIndex)
{
    Core core = getCore();

    Template_Dto templateDto = core.TemplateItemRead(templateId);
    
    if (core.Advanced_TemplateDisplayIndexChangeDb(templateId, displayIndex)) 
    {
        foreach (SiteName_Dto site in templateDto.DeployedSites) 
        {
            core.Advanced_TemplateDisplayIndexChangeServer(templateDto.microtingUId, site.siteId, displayIndex);
        }
    }
        
}
```

```delphi
uses System.SysUtils, System.Classes, Generics.Collections,
  Core, Classes;

procedure SetDisplayIndexForTemplate(templateId: integer; displayIndex: integer);
var
  core: TCore;
  templateDto: TTemplate_Dto;
  site: TSiteName_Dto;
begin
  core := GetCore;

  templateDto := core.TemplateItemRead(templateId);
  if (core.Advanced_TemplateDisplayIndexChangeDb(templateId, displayIndex)) then
  begin
      for site in templateDto.DeployedSites do
        core.Advanced_TemplateDisplayIndexChangeServer(templateId, site.SiteUId, displayIndex);
  end;
end;
```

# Using results

## Getting cases pr template as object

```cs
using eFormCore;
using eFormShared;

public List<Case> ListCasesForTemplate(int templateId)
{
    Core core = getCore();

    return core.CaseReadAll(templateId, null, null);
        
}
```

```delphi
uses System.SysUtils, System.Classes, Generics.Collections,
  Core, Classes, DateUtils;

function ListCasesForTemplate(templateId: integer) : TObjectList<TCase>;
var
  core: TCore;
  startDate: TDateTime;
  endDate: TDateTime;
begin
  core := GetCore;

  startDate := IncYear(Date, -10);
  endDate := IncYear(Date, 10);

  result := core.CaseReadAll(templateId, startDate, endDate);
end;
```

## Getting cases pr template as csv

```cs
using eFormCore;

public String Csv(int templateId)
{
    Core core = getCore();

    string file_name = $"{templateId}_{DateTime.Now.Ticks}.csv";
    string file_path = $"~/bin/output/{file_name}";
    // file_path is the location where you want the output file stored.
    string image_path = string.Format("{0}://{1}{2}",Request.Url.Scheme, 
    Request.Url.Authority, Url.Content("~")) + "output/dataFolder/"; 
    // image_path is the location where pictures/signatures are stored
    core.CasesToCsv(templateId, null, null, file_path, image_path);

    return file_path;
}
```

```delphi
uses System.SysUtils, System.Classes, Generics.Collections,
  Core, Classes, DateUtils;

function Csv(templateId: integer): string;
var
  core: TCore;
  file_path: string;
  image_path: string;

  startDate: TDateTime;
  endDate: TDateTime;
begin

  core := GetCore;

  startDate := IncYear(Date, -10);
  endDate := IncYear(Date, 10);


  file_path := 'output path with filename where csv will be saved to';
  image_path := 'location where pictures/signatures are stored';

  core.CasesToCsv(templateId, startDate, endDate, file_path, image_path);

  result := file_path;
end;
.
```
 
## Getting a single case

```cs
using eFormCore;
using eFormData;

public ReplyElement Edit(int caseId)
{
    Core core = getCore();

    Case_Dto caseDto = core.CaseReadByCaseId(caseId);
    string microting_uuid = caseDto.MicrotingUId;
    string microting_check_uid = caseDto.CheckUId;
    return core.CaseRead(microting_uuid, microting_check_uid);
}
```

```delphi
uses System.SysUtils, Core, Classes, MainElement;

function Edit(caseId: integer): TReplyElement;
var
  core: TCore;
  caseDto: TCase_Dto;
  microting_uuid: string;
  microting_check_uid: string;
begin
  core := TCore.Create;

  caseDto := core.CaseReadByCaseId(caseId);
  microting_uuid := caseDto.MicrotingUId;
  microting_check_uid := caseDto.CheckUId;
  result := core.CaseRead(microting_uuid, microting_check_uid);
end;
```

## Saving modified results for a case

```cs
using eFormCore;

public bool Update(int caseId)
{
    Core core = getCore();

    List<string> checkListValueList = new List<string>();
    List<string> fieldValueList = new List<string>();

    
    // For each check list value do
    checkListValueList.Add($"{id_of_check_list}|{check_list_value}");
    
    // For each field value do
    fieldValueList.Add("{field_value_id}|{field_value}");
    
    // if the field is a MultiSelect with options [1,2,3,4] and 
    // option 1 and 3 was selected, the result needs to look like
    fieldValueList.Add("{field_value_id}|1|3");
        
    return core.CaseUpdate(caseId, fieldValueList, checkListValueList);
}
```

```delphi
Delphi code is comming here soon.
```

This asumes that you are saving results coming from a form post.

# "Workers"

## FAQ

## List all "workers"

```cs
using eFormCore;
using eFormShared;

public List<Site_Dto> CreateWorker()
{
    Core core = getCore();
    
    return core.SiteReadAll(false);     
}

```

```delphi
Delphi code is comming here soon.
```

## Create a new "worker"

```cs
using eFormCore;
using eFormShared;

public Site_Dto CreateWorker()
{
    Core core = getCore();
    
    return core.SiteCreate(siteName, userFirstName, userLastName, null);
}

```

```delphi
Delphi code is comming here soon.
```

<aside class="warning">
DO NOT SET THE userEmail TO ANYTHING OTHER THAN null!
</aside>

## Edit a "worker"

```cs
using eFormCore;
using eFormShared;

public bool UpdateWorker()
{
    Core core = getCore();
    
    Site_Dto siteDto = core.SiteRead(id); // id of the worker
    Worker_Dto workerDto = core.Advanced_WorkerRead((int)siteDto.WorkerUid);
    string userFirstName = "myNewFirstName";
    string userLastName = "myNewLastName";
    
    string fullName = userFirstName + " " + userLastName;
    return core.SiteUpdate(id, fullName, userFirstName, userLastName, workerDto.Email);       
}

```

```delphi
Delphi code is comming here soon.
```

<aside class="warning">
DO NOT SET THE userEmail TO ANYTHING OTHER THAN workerDto.Email!
</aside>

## Request new OTP

```cs
using eFormCore;
using eFormShared;

public eFormShared.Site_Dto GetOtp()
{
    Core core = getCore();
    
    return core.SiteReset(id); // id of the worker
}

```

```delphi
Delphi code is comming here soon.
```

## Deleting a "worker"

```cs
using eFormCore;
using eFormShared;

public bool DeleteWorker()
{
    Core core = getCore();
    
    SiteName_Dto siteNameDto = core.Advanced_SiteItemRead(id); // id of the worker
    
    return core.SiteDelete(siteNameDto.SiteUId);    
}

```

```delphi
Delphi code is comming here soon.
```

# (Adv)Events

# (Adv)Sites

## List all sites

```cs
using eFormCore;
using eFormShared;

public List<SiteName_Dto> ListAllSites()
{
    Core core = getCore();
    
    return core.Advanced_SiteItemReadAll();     
}

```

```delphi
Delphi code is comming here soon.
```

## Create a site

```cs
using eFormCore;
using eFormShared;

public bool CreateSite()
{
    Core core = getCore();
    
    SiteName_Dto siteNameDto = core.Advanced_SiteItemRead(id); // id of the worker
    
    return core.Advanced_SiteItemDelete(siteNameDto.SiteUId);    
}

```

```delphi
Delphi code is comming here soon.
```

## Update a site

```cs
using eFormCore;
using eFormShared;

public bool UpdateSite(int siteId, string siteId)
{
    Core core = getCore();
    
    SiteName_Dto site = core.Advanced_SiteItemRead(siteId);
    core.Advanced_SiteItemUpdate((int)site.SiteUId, siteId);   
}

```

```delphi
Delphi code is comming here soon.
```

## Delete a site

```cs
using eFormCore;
using eFormShared;

public bool DeleteSite(siteId)
{
    Core core = getCore();
    
    return core.Advanced_SiteItemDelete(siteId);
}

```

```delphi
Delphi code is comming here soon.
```

# (Adv)Units

## List all units

```cs
using eFormCore;
using eFormShared;

public List<Site_Dto> CreateWorker()
{
    Core core = getCore();
    
    return core.SiteReadAll(false);     
}

```

```delphi
Delphi code is comming here soon.
```

## Request a new OTP

# (Adv)Workers
  
## List all workers

```cs
using eFormCore;
using eFormShared;

public List<Site_Dto> CreateWorker()
{
    Core core = getCore();
    
    return core.SiteReadAll(false);     
}

```

```delphi
Delphi code is comming here soon.
```

## Create a worker

## Update a worker

## Delete a worker

# (Adv)EntitySearch

## Listing all groups

```cs
using eFormCore;
using eFormShared;

public EntityGroupList ListAllEntityGroups()
{
    Core core = getCore();
    
    return core.Advanced_EntityGroupAll("", "", 0, 100, "EntitySearch");
    // No sorting, can sort by either id or name
    // No filter is applied
    // We start at page 0
    // We have 100 entries per page.
}

```

```delphi
Delphi code is comming here soon.
```

## Creating a list

```cs
using eFormCore;
using eFormShared;

public string ListAllEntityGroups()
{
    Core core = getCore();
    
    return core.EntityGroupCreate("EntitySearch", "NameOfMyGroup");    
}

```

```delphi
Delphi code is comming here soon.
```

## Adding items to a list

```cs
using eFormCore;
using eFormShared;

public bool AddItemToEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);
    eG.Add(new EntityItem("ItemName", "ItemDescription", "1", "created")); // Do this for each itm you want to add.
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Updating items in a list

```cs
using eFormCore;
using eFormShared;

public bool UpdateItemInEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);
    eG.EntityGroupItemList[0].name = "MyNewName";
    eG.EntityGroupItemList[0].Description = "MyNewDescription";
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Deleting items from a list

```cs
using eFormCore;
using eFormShared;

public bool UpdateItemInEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);   
    eG.EntityGroupItemList.RemoveAt(0);
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Using the list in a eForm template

# (Adv)EntitySelect

## Listing all groups

```cs
using eFormCore;
using eFormShared;

public EntityGroupList ListAllEntityGroups()
{
    Core core = getCore();
    
    return core.Advanced_EntityGroupAll("", "", 0, 100, "EntitySelect");
    // No sorting, can sort by either id or name
    // No filter is applied
    // We start at page 0
    // We have 100 entries per page.
}

```

```delphi
Delphi code is comming here soon.
```

## Creating a list

```cs
using eFormCore;
using eFormShared;

public string ListAllEntityGroups()
{
    Core core = getCore();
    
    return core.EntityGroupCreate("EntitySelect", "NameOfMyGroup");    
}

```

```delphi
Delphi code is comming here soon.
```

## Adding items to a list

```cs
using eFormCore;
using eFormShared;

public bool AddItemToEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);
    eG.Add(new EntityItem("ItemName", "ItemDescription", "1", "created")); // Do this for each itm you want to add.
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Updating items in a list

```cs
using eFormCore;
using eFormShared;

public bool UpdateItemInEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);
    eG.EntityGroupItemList[0].name = "MyNewName";
    eG.EntityGroupItemList[0].Description = "MyNewDescription";
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Deleting items from a list

```cs
using eFormCore;
using eFormShared;

public bool UpdateItemInEntityGroup()
{
    Core core = getCore();
    
    EntityGroup eG = core.EntityGroupRead(entityGroupMUId);   
    eG.EntityGroupItemList.RemoveAt(0);
    
    return core.EntityGroupUpdate(eG);
}

```

```delphi
Delphi code is comming here soon.
```

## Using the list in a eForm template
