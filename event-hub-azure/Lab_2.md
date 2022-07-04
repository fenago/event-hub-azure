
Quickstart: Create an Azure Event Hubs schema registry using Azure portal
=========================================================================

In this lab:

1. Prerequisites
2. Create a schema group
3. Add a schema to the schema group
4. Create a new version of schema
5. Clean up resources


**Azure Schema Registry** is a feature of Event Hubs, which provides a
central repository for schemas for event-driven and messaging-centric
applications. It provides the flexibility for your producer and consumer
applications to **exchange data without having to manage and share the schema**. It also provides a simple governance framework for reusable
schemas and defines relationship between schemas through a grouping
construct (schema groups).

This lab shows you how to create a schema group with schemas in a
schema registry hosted by Azure Event Hubs.



-   The feature isn\'t available in the **basic** tier.
-   If the event hub is in a **virtual network**, you won\'t be able to
    create schemas in the Azure portal unless you access the portal from
    a VM in the same virtual network.


Create a schema group
--------------------------------------------------------------------------------------------------------------------------------------------------------

1.  Navigate to the **Event Hubs Namespace** page.

2.  Select **Schema Registry** on the left menu. To create a schema
    group, select **+ Schema Group** on the toolbar.

    ![Image showing the Schema Registry page in the Azure
    portal](./images/namespace-page.png)

3.  On the **Create Schema Group** page, do these steps:

    1.  Enter a **name** for the schema group.

    2.  For **Serialization type**, pick a serialization format that
        applies to all schemas in the schema group. Currently, **Avro**
        is the only type supported, so select **Avro**.

    3.  Select a **compatibility mode** for all schemas in the group.
        For **Avro**, forward and backward compatibility modes are
        supported.

    4.  Then, select **Create** to create the schema group.

        ![Image showing the page for creating a schema
        group](./images/create-schema-group-page.png)
        

4.  Select the name of the **schema group** in the list of schema
    groups.

    ![Image showing schema group in the list
    selected.](./images/select-schema-group.png)
    
5.  You see the **Schema Group** page for the group.

    ![Image showing the Schema Group
    page](./images/schema-group-page.png)

Add a schema to the schema group
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In this section, you add a schema to the schema group using the Azure
portal.

1.  On the **Schema Group** page, select **+ Schema** on the toolbar.

2.  On the **Create Schema** page, do these steps:

    1.  For **Name**, enter **orderschema**.

    2.  Enter the following **schema** into the text box. You can also
        select file with the schema.


        ```
        {
          "namespace": "com.azure.schemaregistry.samples",
          "type": "record",
          "name": "Order",
          "fields": [
            {
              "name": "id",
              "type": "string"
            },
            {
              "name": "amount",
              "type": "double"
            }
          ]
        }
        ```

    3.  Select **Create**.

3.  Select the **schema** from the list of schemas.

    ![Image showing the schema
    selected.](./images/select-schema.png)

4.  You see the **Schema Overview** page for the schema.

    ![Image showing the Schema Overview
    page.](./images/schema-overview-page.png)

5.  If there are multiple versions of a schema, you see them in the
    **Versions** drop-down list. Select a version to switch to that
    version schema.

Create a new version of schema
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1.  Update the schema in the text box, and select **Validate**. In the
    following example, a new field `description` has been added to the
    schema.

    ![Image showing the Update schema
    page](./images/update-schema.png)

2.  Review validation status and changes, and select **Save**.

    ![Image showing the Review page that shows validation status,
    changes, and save](./images/compare-save-schema.png)
    

3.  You see that `2` is selected for the **version** on the **Schema Overview** page.

    ![Image showing the new version of
    schema](./images/new-version.png)

4.  Select `1` to see the version 1 of the schema.


Clean up resources
------------------

1.  Navigate to the **Event Hubs Namespace** page.
2.  Select **Schema Registry** on the left menu.
3.  Select the **schema group** you created in this quickstart.
4.  On the **Schema Group** page, select **Delete** on the toolbar.
5.  On the **Delete Schema Group** page, type the name of the schema
    group, and select **Delete**.

