---
slug: salesforce-complex-object-xml
title: Salesforce Complex Object XML
---
This task will update or insert multiple records of different types in Salesforce, and create relationships between them. The example below shows how to upload opportunities and opportunity item lines, and link them to a particular account and pricebook.

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity" Key="Name">
    <SalesforceObject>
        <Fields>
            <Field Name="Name" Value="TEST Opp 12345" />
            <Field Name="CloseDate" Value="2016-08-10" />
            <Field Name="StageName" Value="Closed Won" />
            <LookupField Name="AccountId">
                <Criteria Type="Account">
                    <Fields>
                        <Field Name="Name" Value="Test 123" />
                    </Fields>
                </Criteria>
            </LookupField>
            <LookupField Name="Pricebook2Id">
                <Criteria Type="Pricebook2">
                    <Fields>
                        <Field Name="Name" Value="Standard Price Book" />
                    </Fields>
                </Criteria>
            </LookupField>
        </Fields>
        <Relationships>
            <Relationship Type="OpportunityLineItem">
                <SalesforceObject>
                    <Fields>
                        <LookupField Name="Id">
                            <Criteria Type="OpportunityLineItem">
                                <Fields>
                                    <Field Name="OpportunityId" Value="{ParentId}" />
                                    <LookupField Name="PricebookEntryId">
                                        <Criteria Type="PricebookEntry">
                                            <Fields>
                                                <Field Name="Pricebook2.Name" Value="Standard Price Book" />
                                                <Field Name="CurrencyIsoCode" Value="GBP" />
                                                <Field Name="Product2.ProductCode" Value="IPHONE4" />
                                            </Fields>
                                        </Criteria>
                                    </LookupField>
                                </Fields>
                            </Criteria>
                        </LookupField>
                        <Field Name="Description" Value="iPhone 4" />
                        <Field Name="OpportunityId" Value="{ParentId}" />
                        <Field Name="Quantity" Value="1" />
                        <Field Name="UnitPrice" Value="250" />
                        <LookupField Name="PricebookEntryId">
                            <Criteria Type="PricebookEntry">
                                <Fields>
                                    <Field Name="Pricebook2.Name" Value="Standard Price Book" />
                                    <Field Name="CurrencyIsoCode" Value="GBP" />
                                    <Field Name="Product2.ProductCode" Value="IPHONE4" />
                                </Fields>
                            </Criteria>
                        </LookupField>
                    </Fields>
                    <Relationships />
                </SalesforceObject>
            </Relationship>
        </Relationships>
    </SalesforceObject>
</SalesforceObjects>
```

In each of the following sections most of the XML has been omitted to make the samples easier to read. The whole structure of the SalesforceObjects schema is used below as a reference of where fields should be in the object model.

## SalesforceObjects
The SalesforceObjects element represents a collection of objects to upload to Salesforce. This element is the root of the XML document.

| XML Field  | Example  | Field Type  | Input  | Description |
| --- | --- | --- | --- | --- |
| @Type | Opportunity | string | Required | The type of objects contained within the collection. This should correspond with the API name of an object in Salesforce. |
| @Key | Name | string | Optional | Specify the Salesforce API field name of a field to use as the key. Defaults to Id if not specified. The key field will be used to match each SalesforceObject within the collection to existing records in Salesforce, so long as a value is specified for the key field in the Fields collection of the SalesforceObject. If a match is found, the existing record will be updated, otherwise a new record will be created. |
| SalesforceObject | N/A | SalesforceObject | Optional | A record to upload to Salesforce. 0 or more SalesforceObject elements can be included in the collection. See below for more information on the SalesforceObject element. |

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity" Key="Name">
    <SalesforceObject></SalesforceObject>
</SalesforceObjects>
```

## SalesforceObject
The SalesforceObject element represents an individual record to insert or update in Salesforce.

| XML Field  | Example  | Field Type  | Input  | Description |
| --- | --- | --- | --- | --- |
| Fields | N/A | Field or LookupField | Required | A collection of field values to set on the object in Salesforce. The collection can contain either Field or LookupField elements. |
| Relationships | N/A | Relationship | Optional | A collection of child records related to this record. The collection can contain 0 or more Relationship elements. See below for more information on the Relationship element. |

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity">
    <SalesforceObject>
        <Fields></Fields>
        <Relationships></Relationships>
    </SalesforceObject>
</SalesforceObjects>
```

## Field
The Field element represents the value of a field in Salesforce.

| XML Field  | Example  | Field Type  | Input  | Description |
| --- | --- | --- | --- | --- |
| @Name | CloseDate | string | Required | The Salesforce API field name of the field in Salesforce. |
| @Value | 2016-08-10 | string | Optional | The value for the field. |

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity">
    <SalesforceObject>
        <Fields>
            <Field Name="CloseDate" Value="2016-08-10" />
        </Fields>
    </SalesforceObject>
</SalesforceObjects>
```

## LookupField
The LookupField element represents a lookup for the value of an ID field in Salesforce. This is useful where you know the value of a unique identifier for a record, but don't know the ID. The example shown below will lookup the ID of the account with the name 'Test 123', and use this as the value for the AccountId field on an opportunity.

| XML Field  | Example  | Field Type  | Input  | Description |
| --- | --- | --- | --- | --- |
| @Name | AccountId | string | Required | The Salesforce API field name of the field in Salesforce. |
| @Value | string | Optional | If a value is specified, this will be used instead of performing the lookup. |
| Criteria/@Type | Account | string | Required | The type of object to perform the lookup for. This should correspond with the API name of an object in Salesforce. |
| Criteria/Fields | N/A | Field or LookupField | Required | The collection of field values to lookup records based on. At least one field must be specified, only records with field values matching all of those specified will be considered a match. Complex lookups across diferent types of object can be constructed by using further LookupField elements within the Fields collection. |

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity">
    <SalesforceObject>
        <Fields>
            <LookupField Name="AccountId">
                <Criteria Type="Account">
                    <Fields>
                        <Field Name="Name" Value="Test 123" />
                    </Fields>
                </Criteria>
            </LookupField>
        </Fields>
    </SalesforceObject>
</SalesforceObjects>
```

## Relationship
The Relationship element represents an individual record that is a child of the SalesforceObject it is placed in. To create the relationship between the two records, you need make sure you set the value of the field that links the child to it's parent. You can access the Id of the parent record using the {ParentId} variable, as shown in the sample XML below.

| XML Field  | Example  | Field Type  | Input  | Description |
| --- | --- | --- | --- | --- |
| @Type | OpportunityLineItem | string | Required | The type of the child record. This should correspond with the API name of an object in Salesforce. |
| SalesforceObject | N/A | SalesforceObject | Required | The child record. See above for more information on the SalesforceObject element. |

```xml
<?xml version="1.0"?>
<SalesforceObjects xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Type="Opportunity">
    <SalesforceObject>
        <Relationships>
            <Relationship Type="OpportunityLineItem">
                <SalesforceObject>
                    <Fields>
                        <Field Name="OpportunityId" Value="{ParentId}" />
                    </Fields>
                </SalesforceObject>
            </Relationship>
        </Relationships>
    </SalesforceObject>
</SalesforceObjects>
```