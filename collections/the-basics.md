---
description: The basics of a collection configuration in Konstrukt, the back office UI builder for Umbraco.
---

# The Basics

There is a lot that can be configured from the collection config, but what follows are the core basics. You can find more configuration options about specific topics from the other configuration sections in the main menu.

## Defining a collection

You define a collection by calling one of the `AddCollection` methods on a given [`Tree`](../areas/trees.md) or parent [`Folder`](../areas/folders.md) config builder instance.

#### **AddCollection&lt;TEntityType&gt;(Lambda idFieldExpression, string nameSingular, string namePlural, string description, Lambda collectionConfig = null) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Adds a collection to the given container with the given names and description and default icons. An ID property accessor expression is required so that Konstrukt knows which property is the ID property.

````csharp
// Example
folderConfig.AddCollection<Person>(p => p.Id, "Person", "People", "A collection of people", collectionConfig => {
    ...
});
````

#### **AddCollection&lt;TEntityType&gt;(Lambda idFieldExpression, string nameSingular, string namePlural, string description, string iconSingular, string iconPlural, Lambda collectionConfig = null) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Adds a collection to the given container with the given names, description and icons. An ID property accessor expression is required so that Konstrukt knows which property is the ID property.

````csharp
// Example
folderConfig.AddCollection<Person>(p => p.Id, "Person", "People", "A collection of people", "icon-umb-users", "icon-umb-users", collectionConfig => {
    ...
});
````

## Changing a collection alias

#### **SetAlias(string alias) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the alias of the collection.  

**Optional:** When creating a new collection, an alias is automatically generated from the supplied name for you, however you can use the `SetAlias` method to override this should you need a specific alias.

````csharp
// Example
collectionConfig.SetAlias("person");
````

## Changing a collection icon color

#### **SetIconColor(string color) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the collection icon color to the given color.  Possible options are `black`, `green`, `yellow`, `orange`, `blue` or `red`.

````csharp
// Example
collectionConfig.SetIconColor("blue");
````

## Defining an entity name

Within Umbraco it is expected that an entity has a name property so we need to let Konstrukt know which property to use for the name or if our entity doesn't have a single name property, then how to construct a name from an entities other properties. We do this by using either the `SetNameProperty` or `SetNameFormat` methods on a `Collection` config builder instance.

#### **SetNameProperty(Lambda nameProperytyExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of your entity to use as the name property. Property must be of type `string`. By defining a property as the name property, it's value will be used as the label for the entity in things like trees and list views and will also be editable in the header region of the editor interface. The property will also automatically be added to the searchable properties collection and be used for the default sort property.

````csharp
// Example
collectionConfig.SetNameProperty(p => p.Name);
````

#### **SetNameFormat(Lambda nameFormatExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets a format expression to use to dynamically create a label for the entity in things like trees and list views. By providing a name format it is assumed there is no single name property available on the entity and as such none of the default behaviours descriped for the `SetNameProperty` method will apply.

````csharp
// Example
collectionConfig.SetNameFormat(p => $"{p.FirstName} {p.LastName}");
````

## Defining a default sort order

#### **SetSortProperty(Lambda sortPropertyExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of our entity to sort against, defaulting to ascending sort direction.

````csharp
// Example
collectionConfig.SetSortProperty(p => p.FirstName);
````

#### **SetSortProperty(Lambda sortPropertyExpression, SortDirection sortDirection) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of our entity to sort against in the provided sort direction.

````csharp
// Example
collectionConfig.SetSortProperty(p => p.FirstName, SortDirection.Descending);
````

## Defining time stamp properties

#### **SetDateCreatedProperty(Lambda dateCreatedProperty) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of our entity to use as the date created property. Property must be of type `DateTime`. When set and a new entity is saved via the Konstrukt repository, then the given field will be populated with the current date and time.

````csharp
// Example
collectionConfig.SetDateCreatedProperty(p => p.DateCreated);
````

#### **SetDateModifiedProperty(Lambda dateCreatedProperty) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of our entity to use as the date modified property. Property must be of type `DateTime`. When set and an entity is saved via the Konstrukt repository, then the given field will be populated with the current date and time.

````csharp
// Example
collectionConfig.SetDateModifiedProperty(p => p.DateModified);
````

## Configuring soft deletes

By default in Konstrukt any entity that is deleted via the Konstrukt repository is completely removed from the system. In some occasions however you may wish to keep the records in the data repository but just mark them as deleted so that they don't appear in the UI. This is where the `SetDeletedProperty` method comes in handy.

#### **SetDeletedProperty(Lambda deletedPropertyExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets which property of our entity to use as the deleted property flag. Property must be of type `boolean` or `int`. When a deleted property is set, any delete actions will set the deleted flag instead of actualy deleting the entity. For `boolean` based properties, deleted entities will have a value of `True` when deleted, and for `int` based properties, deleted entities will have a UTC UNIX timestamp value of the date the entity was deleted. In addition, any fetch actions will also pre-filter out any deleted entities.

````csharp
// Example
collectionConfig.SetDeletedProperty(p => p.Deleted);
````


## Disabling create, update or delete features

#### **DisableCreate() : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to create entities on the current collection. An entity could be created via code and then only editing is allowed in the UI for example.

````csharp
// Example
collectionConfig.DisableCreate();
````

#### **DisableCreate(Predicate&lt;KonstruktCollectionPermissionContext&gt; disableExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to create entities on the current collection if the given runtime predicate is true. An entity could be created via code and then only editing is allowed in the UI for example.

````csharp
// Example
collectionConfig.DisableCreate(ctx => ctx.UserGroups.Any(x => x.Alias == "editor"));
````

#### **DisableUpdate() : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to update entities on the current collection. An entity can be created, but further editing is not allowed. 

````csharp
// Example
collectionConfig.DisableUpdate();
````

#### **DisableUpdate(Predicate&lt;KonstruktCollectionPermissionContext&gt; disableExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to update entities on the current collection if the given runtime predicate is true. An entity can be created, but further editing is not allowed. 

````csharp
// Example
collectionConfig.DisableUpdate(ctx => ctx.UserGroups.Any(x => x.Alias == "editor"));
````

#### **DisableDelete() : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to delete entities on the current collection. Useful if the data needs to be retained and visible. See also [defining a deleted flag](#defining-a-deleted-flag).

````csharp
// Example
collectionConfig.DisableDelete();
````

#### **DisableDelete(Predicate&lt;KonstruktCollectionPermissionContext&gt; disableExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Disables the option to delete entities on the current collection if the given runtime predicate is true. Useful if the data needs to be retained and visible. See also [defining a deleted flag](#defining-a-deleted-flag).

````csharp
// Example
collectionConfig.DisableDelete(ctx => ctx.UserGroups.Any(x => x.Alias == "editor"));
````

#### **MakeReadOnly() : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the collection as read only and disables any CRUD operations from being performed on the collection via the UI.

````csharp
// Example
collectionConfig.MakeReadOnly();
````

#### **MakeReadOnly(Predicate&lt;KonstruktCollectionPermissionContext&gt; disableExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the collection as read only if the given runtime predicate is true and disables any CRUD operations from being performed on the collection via the UI.

````csharp
// Example
collectionConfig.MakeReadOnly(ctx => ctx.UserGroups.Any(x => x.Alias == "editor"));
````

## Set the visibility of the collection

#### **SetVisibility(Predicate&lt;KonstruktCollectionVisibilityContext&gt; visibilityExpression) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the runtime visibility of the collection.

````csharp
// Example
collectionConfig.SetVisibility(ctx => ctx.UserRoles.Any(x => x.Alias == "editor"));
````

## Changing a collection connection string

By default Konstrukt will use the Umbraco connection string for it's database connection however you can change this by calling the `SetConnectionString` method on a `Collection` config builder instance.

#### **SetConnectionString(string connectionStringName) : KonstruktCollectionConfigBuilder&lt;TEntityType&gt;**

Sets the connection string name for the given collection repository.

````csharp
// Example
collectionConfig.SetConnectionString("myConnectionStringName");
````