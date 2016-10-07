---
ID: 139
post_title: Executing Queries
author: Jorge Vieira
post_date: 2016-10-04 10:57:35
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/documentation/executing-queries/
published: true
sidebar:
  - ""
footer:
  - ""
header_title_bar:
  - ""
header_transparency:
  - ""
---
[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='top center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#545355' overlay_pattern='' overlay_custom_pattern='']

[av_heading heading='Query Instances
Send Commands to a Database' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 5
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']

[/av_section][av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']
[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Doc 1']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
To send commands to a database - whether to fetch, insert, delete or update objects - you will create, configure and execute instances of <code>Query</code>. A<code>Query</code> allows you to create database operations in Dart code in the domain of your application, as opposed to writing SQL. <code>Query</code>s are executed against a <code>ModelContext</code>. By default, every <code>Query</code> uses <code>ModelContext.defaultContext</code>, but this can be changed during the instantiation of a <code>Query</code>.

(Note that raw SQL may be sent to a database in an Aqueduct application using instances of <code>PersistentStore</code> directly. Queries are meant for data operations, not database management.)

Instances of <code>Query</code> are generic. The type parameter indicates the entity you are fetching, inserting, deleting or updating. The type parameter must be a subclass of <code>Model</code> for which a fully formed <code>ModelEntity</code> exists. When a <code>Query</code> is created, it sets its <code>entity</code> property to <code>ModelEntity</code> that represents its type parameter:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query();
query.entity.instanceType; // -&gt; ClassMirror on 'User' ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code>DataModel</code> that a <code>Query</code>'s <code>entity</code> must belongs to same <code>ModelContext</code> as the <code>Query</code>'s <code>context</code>.

A <code>Query</code> has four basic execution methods: <code>fetch</code>, <code>update</code>, <code>insert</code>, <code>delete</code>. These methods will perform a database operation based on the information configured in the <code>Query</code>.
<ul>
 	<li><code>fetch</code> will retrieve data from a database (it is equivalent to the SQL operation <code>SELECT</code>). Rows are returned in instances of the <code>Query</code>'s generic type.</li>
 	<li><code>update</code> will modify existing data in a database (it is equivalent to the SQL operation <code>UPDATE</code>). Rows are returned in instances of the <code>Query</code>'s generic type.</li>
 	<li><code>insert</code> will add new data to a database (it is equivalent to the SQL operation <code>INSERT</code>). Rows are returned in instances of the <code>Query</code>'s generic type.</li>
 	<li><code>delete</code> will remove data from a database (it is equivalent to the SQL operation <code>DELETE</code>). The number of rows deleted is returned as an integer.</li>
</ul>
A <code>Query</code> has properties that dictate the information that will be used in their execution methods. These properties will impact which objects get fetched, the data that gets sent to the database, the order that data is returned in, and so on. A <code>Query</code> is not instantiated with a pre-determined execution method; the same <code>Query</code> can be used to <code>fetch</code> objects and then used to <code>delete</code> objects without any changes. The <code>Query</code> does not maintain any state about execution methods executed or results from an execution of it.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="insertingdata">1 Inserting Data with a Query</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
To configure a query to insert an object into a database, you must first create an instance of <code>Query</code>. The type argument must be the instance type of the entity you are inserting. To specify the values to insert, every <code>Query</code> has a <code>values</code> properties. Setting values in this instance configures the values the<code>Query</code> will insert when it is executed. For example, if there was a <code>User</code> entity with a <code>name</code> and <code>email</code>, you would configure a <code>Query</code> to insert an instance of<code>User</code> like so:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var query = new Query() ..values.name = "Bob" ..values.email = "bob@stablekernel.com";</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code>values</code> property of a <code>Query</code> will automatically be created as soon as you access it. You may also provide an instance to <code>values</code> directly; this comes in handy when you are getting an object from an HTTP request body:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User() ..readMap(requestBody);
var query = new Query() ..values = user; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
(You may optionally configure a <code>Query</code>'s <code>valueMap</code> to supply values for an insert query, but this is less desirable because <code>valueMap</code> is stringly-typed whereas <code>values</code> is both key-checked and type-checked. You may only use one or the other. If both <code>valueMap</code> and <code>values</code> are configured, the <code>Query</code>'s behavior is undefined.)

Once a <code>Query</code> has been configured with values, it may be executed as an insert command. The value returned from an insert will be the newly inserted instance inside a <code>Future</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query() ..values.name = "Bob" ..values.email = "bob@stablekernel.com";
User user = await query.insert(); user.name; // "Bob" user.email; // "bob@stablekernel.com" user.id; // 1 ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Since most model objects have an auto-incrementing primary key, it is important to get the returned model object from an insert execution, as it will contain the generated primary key value.

Note that only the data explicitly set in <code>values</code> (or <code>valueMap</code>) is sent to the database. Unset values are <em>not</em> sent as <code>null</code> and entirely omitted from the insert command.

If an insert query fails because of a conflict - a unique constraint is violated - Aqueduct will automatically generate an <code>HTTPResponseException</code> with a 409 status code. This default behavior means that you don't have to check for the specific error of unique constraint violation and Aqueduct will abandon execution and return a 409 'Conflict' response to the calling client.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="updatingdata">2 Updating Data with a Query</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Updating data with a <code>Query</code> is similar to inserting data in that the data you set the <code>values</code> of a <code>Query</code> for data you want to change. The type parameter for the <code>Query</code> indicates which entity - and therefore which database table - will get updated when the query is executed.

An update query can - and likely should be - restricted to a single row or multiple rows. This is done by using the <code>matchOn</code> or <code>predicate</code> property of a<code>Query</code> - which gets translated into the <em>where clause</em> of the underlying SQL statement.

Because a <code>Query</code>s <code>predicate</code> and <code>matchOn</code> properties can be used for update, fetch and delete queries, and they are such a large topic, there is a separate section reserved specifically for those properties. For the purpose of understanding update queries, know that the <code>matchOn</code> and <code>predicate</code>properties allow you to identify specific rows in a database.

By executing an update query, the specified rows will get new values for all of the values in <code>values</code> (or <code>valueMap</code>). Only the <code>values</code> explicitly specified in the query will be modified by the update query. In effect, this means you do not have to fetch an object before updating it - you can provide only the values you wish to update and the remainder of the row is unmodified.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart // A Query that will change any user's whose name is 'Bob' to 'Fred' var query = new Query() ..values.name = "Fred" ..matchOn.name = whereEqualTo("Bob");
List bobsThatAreNowFreds = await query.update(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
An update query modifies rows in the database and returns every modified row as a result. Only rows that were updated will be returned and they will be returned as instances of the appropriate <code>Model</code> subclass. There is a variant to <code>Query</code>'s <code>update</code> method named <code>updateOne</code>. The <code>updateOne</code> method will build and execute a SQL query in the same way a normal <code>update</code> does, however, it will return you a single instance that was updated instead of a list. This is convenience method for the caller to get back a single instance instead of a list:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart // Update user with id = 1 to have the name 'Fred' var query = new Query() ..values.name = "Fred" ..matchOn.id = whereEqualTo(1);
User updatedUser = await query.updateOne(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code>updateOne</code> method will return <code>null</code> if no rows were updated. It is important to note that if <code>updateOne</code> is used and more than one row is updated,<code>updateOne</code> will throw an exception and the changes to the data <em>are not reversible</em>. Because this is likely a mistake, this is considered an error, hence the exception is thrown. It is up to the programmer to recognize whether or not a particular <code>updateOne</code> query would impact multiple rows.

If an update violates a unique property, an <code>HTTPResponseException</code> with status code 409 will be thrown.

Update queries have a safety feature that prevents you from accidentally updating every row. If you try to execute a <code>Query</code> to do an update and no<code>predicate</code> or <code>matchOn</code> property is defined, the default behavior of <code>Query</code> will throw an exception prior to carrying out the request. If you explicitly want to update every instance of some entity (that is, every row of a table), you must set the <code>Query</code>'s <code>confirmQueryModifiesAllInstancesOnDeleteOrUpdate</code> to <code>true</code>prior to execution. (This property defaults to <code>false</code>.)
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="deletingdata">3 Deleting Data with a Query</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
A delete <code>Query</code> will delete rows from a database for the entity specified by its type argument. Like update and fetch queries, you may specify a row or rows using <code>matchOn</code> and <code>predicate</code> properties of the <code>Query</code>. The result of a delete operation will be a <code>Future</code> with the number of rows deleted.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query() ..matchOn.id = 1;
int usersDeleted = await query.delete(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Also like update queries, delete queries have a safety feature that prevents you from accidentally deleting every row in a table. If you try to execute a<code>Query</code> to do an update and no <code>predicate</code> or <code>matchOn</code> property is defined, the default behavior of <code>Query</code> will throw an exception prior to carrying out the request. If you explicitly want to delete every instance of some entity (that is, every row of a table), you must set the <code>Query</code>'s<code>confirmQueryModifiesAllInstancesOnDeleteOrUpdate</code> to <code>true</code> prior to execution.

Any properties set in the query's <code>values</code> or <code>valueMap</code> are ignored when executing a delete.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="fetchtingdata">4 Fetching Data with a Query</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Of the four basic operations of a <code>Query</code>, fetching data is the most configurable and powerful. In its simplest form, a fetch query will return matching instances for an entity (that is, matching rows from a table). A simple <code>Query</code> that would fetch every instance of some entity looks like this:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query();
List allUsers = await query.fetch(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
A fetch <code>Query</code> uses its <code>matchOn</code> and <code>predicate</code> to filter the result set, just like delete and update queries. Any properties set in the query's <code>values</code> or<code>valueMap</code> are ignored when executing a fetch. In addition to fetching a list of instances from a database, you may also fetch a single instance with<code>fetchOne</code>. If no instance is found, <code>null</code> is returned. (If more than one instance is found, an exception is thrown.)
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query() ..matchOn.id = 1;
User oneUser = await query.fetchOne(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Fetches can be limited to a number of instances by setting the <code>fetchLimit</code> property of a <code>Query</code>. You may also set the <code>offset</code> of a <code>Query</code> to skip the first<code>offset</code> number of rows. Between <code>fetchLimit</code> and <code>offset</code>, you can implement naive paging. However, this type of paging suffers from a number of problems and so there is a built-in paging mechanism covered in a later section.

Results of a fetch can be sorted by adding <code>SortDescriptor</code>s to a <code>Query</code>'s <code>sortDescriptors</code> property. A <code>SortDescriptor</code> has a key (the name of a property on a entity) and the order in which rows should be sorted (ascending or descending). For example, the following would return users in alphabetical order based on their name, from A-Z:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query() ..sortDescriptors = [new SortDescriptor("name", SortDescriptorOrder.ascending)];
List orderedUsers = await query.fetch(); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
A <code>Query</code> can have multiple sort descriptors. Subsequent sort descriptors are used to break ties in previous sort descriptors. The order sort descriptors are applied in is the order they are listed in the <code>sortDescriptors</code> list.

Fetch queries can fetch an entity's relationships (these are carried out as <em>database joins</em> by the <code>PersistentStore</code>). Because this is a more complex topic, this discussion is reserved for a later section.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="specifyingresults">5 Specifying Result Properties</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
When executing a query that returns model objects (i.e., insert, update and fetch queries), you may configure which properties are actually fetched for each instance. Every entity has a set of <code>defaultProperties</code>. If you do not specify exactly which properties to be fetched, these an instance will have all of the properties in its entity's <code>defaultProperties</code> set in its <code>backingMap</code>.

The default properties of an entity are all attributes declared in the persistent type that do not explicitly have the <code>omitByDefault</code> <code>AttributeHint</code> set to true and all <code>InverseRelationship</code> properties. (In other words, every actual column on the corresponding database table that you haven't specifically marked to be ignored.)

Properties like a hashed password and salt are likely candidates to be marked as <code>omitByDefault</code>, as you don't typically want to return that information in an HTTP response body. Marking a attribute as such frees you from having to exclude it each time you create a <code>Query</code>.

If you wish to specify which properties are fetched into an instance when performing a <code>Query</code>, you may set the <code>Query</code>'s <code>resultProperties</code>. This<code>List</code> is the name of each property you wish to be fetched. This list is the exact set of properties to be fetched - it does not also include properties from an entity's <code>defaultProperties</code>. This property is useful when you want to limit the properties fetched from a query. (Bear in mind, an un-fetched property will be omitted from the <code>Map</code> as a result of serialized model object.) Additionally, setting the <code>resultProperties</code> of a <code>Query</code> is the only way to fetch properties that are marked as <code>omitByDefault</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User {} class _User { @primaryKey int id;
String name; String gender;
@AttributeHint(unique: true, indexed: true) String email;
@AttributeHint(omitByDefault: true) String hashedPassword;
@AttributeHint(omitByDefault: true) String salt; }
var query = new Query() ..resultProperties = ["id", "email", "hashedPassword", "salt"] ..matchOn.email = whereEqualTo("bob@stablekernel.com");
var bob = await query.fetchOne(); bob.backingMap == { "id" : 1, "email" : "bob@stablekernel.com", "hashedPassword" : "ABCD1234ABCD", "salt" : "ABCD4321" }; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
If you specify a property that doesn't exist for an entity in <code>resultProperties</code>, you will get an exception when the <code>Query</code> is executed.

You may not add a 'hasMany' or 'hasOne' relationship to <code>resultProperties</code>, as this mechanism is achieved by other functionality on a <code>Query</code>. If you do add a 'hasOne' or 'hasMany' relationship property name to the list of <code>resultProperties</code>, an exception will be thrown when the query is executed.

Note that if you also set the top-level <code>resultProperties</code> of a query and omit the primary key of the entity, it will automatically be added.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="pagingfetchedresults">6 Paging Fetched Result Sets</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
In larger data sets, it may make sense to only return a portion of rows from a database. For example, in a social media application, a user could have thousands of pieces of content they had created over many years. The likely use case for fetching their content would be to grab only the most recent content, and only grab earlier content as necessary. There are many ways to accomplish paging, and the right solution oftentimes depends on the context and may require additional services beyond just a single database - Aqueduct doesn't pretend to fully solve this problem. Aqueduct does provide two mechanisms on <code>Query</code> instances for building queries that can fetch a subset of rows within a certain range.

First, naive paging can be accomplished using the <code>fetchLimit</code> and <code>offset</code> properties of a <code>Query</code>. For example, if a table contains 100 rows, and you would like to vend them out 10 at a time to a client, each query would have a value of 10 for its <code>fetchLimit</code>. The first query would have an <code>offset</code> of 0, then 10, then 20, and so on. Especially when adding <code>sortDescriptors</code> to a <code>Query</code>, this type of paging can be effective.

One of the drawbacks to this type of paging is that it can skip or duplicate rows if rows are being added or deleted while fetching subsequent pages. For example, if a table again contains 100 rows, and you have made two queries to fetch the first 20, the next query (to grab rows 20-29) should have an<code>offset</code> of 20 and a <code>fetchLimit</code> of 10. If a row is inserted prior to executing the third query that gets rows 20-29, the row that was previously #19 gets moved into the #20 slot. Thus, the query to get rows 20-29 will contain a duplicate from the previous query. Likewise, if a row is deleted from within the first 20 rows, what would have been row #20 moves into slot #19. When fetching rows 20-29, #19 is not fetched and it is skipped.

Therefore, <code>Query</code> has a property named <code>pageDescriptor</code> (an instance of <code>QueryPage</code>) to better handle paging and avoid the problem of sliding rows. A<code>QueryPage</code> works by using the value of some property as its starting point for results. For example, given an entity that has a <code>dateCreated</code> property, a<code>QueryPage</code> could specify that the 'starting' point of the result set was at a specific date. A <code>QueryPage</code> also indicates whether the rows should be fetched before or after the starting point. In the context of a <code>dateCreated</code> property, this would mean whether the query would fetch rows newer than the starting point or older than the starting point.

A <code>QueryPage</code>, then, has three pieces of information: the name of the property on the entity that the query is paging by, the value for the property that indicates the starting point, and a direction to go from that starting point. Respectively, these properties are <code>propertyName</code>, <code>boundingValue</code> and <code>order</code>.

For example, consider an entity that has a <code>createdDate</code> property:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class Task extends Model implements _Task {} class _Task { @primaryKey int id; String text;
@AttributeHint(indexed: true) DateTime dateCreated; } ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
In an application that displays a timeline of <code>Task</code>s, a user would most likely want to see their most recent five tasks first. Then, if they choose to continue browsing, the next five tasks after that, and then the next five and so on. Thus, the <code>order</code> must be <code>SortOrder.descending</code> - later dates are 'less than' more recent dates and the <code>propertyName</code> must be <code>dateCreated</code>. This particular query would fetch the first five <code>Task</code>s:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var query = new Query() ..fetchLimit = 5 ..pageDescriptor = new QueryPage(SortOrder.descending, "dateCreated"); var firstFive = await query.fetch();</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice that <code>fetchLimit</code> is set, otherwise all instances of the entity would be fetched.

When fetching the first page, like in this example, the value of <code>boundingValue</code> should be <code>null</code>. The <code>null</code> value indicates that the query will grab the first<code>fetchLimit</code> number of rows after ordering by the <code>propertyName</code>. Once a page has been fetched, the last value for the page property should be used as the <code>boundingValue</code> for the next.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var firstFive = await query.fetch();
var nextPageQuery = new Query() ..fetchLimit = 5 ..pageDescriptor = new QueryPage(SortOrder.descending, "dateCreated", boundingValue: firstFive.last.dateCreated); var nextFive = await nextPageQuery.fetch();
```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code>boundingValue</code> is <em>not</em> inclusive. For example, if the last task in first page if tasks has a <code>dateCreated</code> of October 5, 2013, the next page would be fetched using <code>boundingValue</code> of that same date. The first result in the next page would be <em>after</em> October 5, 2013. (Obviously, if the <code>order</code> is<code>SortOrder.ascending</code>, the next page would be <em>before</em> that date.)

The <code>boundingValue</code> will be encoded according to its type when sent to the underlying database, so you may pass normal Dart types like <code>DateTime</code> and<code>int</code>. The order of the rows returned by a query that has been paged will always match the order defined by the <code>QueryPage</code>'s <code>order</code>. If the query finds no more instances - that is, it runs out of data - the query will simply return zero model objects.

The value <code>null</code> should be the <code>boundingValue</code> when fetching the first page. It is also permissible to use a value that is known to be well outside of the range of possible values - for example, a date in the year 3000 is unlikely to exclude the most recent task object.

It is a good idea to add an index <code>AttributeHint</code> to any property that is used in a <code>QueryPage</code>.

Do not use the <code>offset</code> property of a <code>Query</code> when performing pages, as the property and bounding value already provide an offset into the data.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="filteringresults">7 Filtering Results of a Fetch Operation</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
More often than not, fetching every instance of some entity doesn't make sense. Instead, the desired result of a fetch is a specific object or set of objects matching some condition. Aqueduct offers two ways to perform this filtering, both of which translate to a SQL <em>where clause</em>.

The first option is the least prohibitive, the most prone to error and the most difficult to maintain: a <code>Query</code>'s <code>predicate</code> property. A <code>Predicate</code> is effectively a <code>String</code> that is added to the underlying query's where clause. A <code>Predicate</code> has two properties, a <code>format</code> string and a <code>Map&lt;String, dynamic&gt;</code> of parameter values. The <code>format</code> string can (and should) parameterize any input values. Parameters are indicated in the format string using the <code>@</code> token:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart // Creates a predicate that would only include instances where some column "id" is less than 2 var predicate = new Predicate("id &lt; @id", {"id" : 2});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The text following the <code>@</code> token may contain <code>[A-Za-z0-9_]</code>. The resulting where clause will be formed by replacing each token with the matching key in the parameters map. The value is not transformed in any way, so it must be the appropriate type for the property it is filtering by. If a key is not present, an exception will be thrown. Extra keys will be ignored.

A raw <code>Predicate</code> like this one suffers from a few issues. First, predicates are <em>database specific</em> that is, after the values from the <code>parameters</code> are added to the <code>format</code> string, the resulting <code>String</code> is evaluated as-is by the underlying database. Perhaps more importantly, there is nothing to verify that the<code>Predicate</code> refers to the appropriate column names or that the data in the <code>parameters</code> is the right type. This can cause chaos when refactoring code, where a simple name change to a property would break a <code>Predicate</code>. This option is primarily intended to be used as a fallback if a Aqueduct is incapable of expressing the desired SQL.

In most cases, the <code>matchOn</code> property of a <code>Query</code> is a much safer and elegant way is to have <code>Query</code> generate a <code>Predicate</code> using the query's <code>matchOn</code>property. The <code>matchOn</code> property allows you to assign <code>MatcherExpression</code>s to the properties of the entity being queried. A <code>MatcherExpression</code> is effectively a conditional to apply to some property and follows the same Hamcrest matcher style that the Dart test framework use. For example, there are<code>MatcherExpression</code>s for checking if two values are equal, if a value is between two other values, or whether a value begins with a certain string.

The <code>matchOn</code> property of a <code>Query</code> has the same interface as the entity of the <code>Query</code> - i.e., it has all of the properties of the object you're fetching. For each property that is assigned a <code>MatcherExpression</code>, the resulting conditional will be built into a generated <code>Predicate</code>. Here's an example of a <code>Query</code> using<code>matchOn</code> to find a <code>User</code> with an <code>id</code> equal to 1:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var query = new Query() ..matchOn.id = whereEqualTo(1);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
(The generated SQL here would be 'SELECT _user.id, _user.name, ... FROM _user WHERE _user.id = 1'.)

All <code>MatcherExpression</code>s are created using one of the <code>where</code> top-level methods in Aqueduct. Other examples are <code>whereGreaterThan</code>, <code>whereBetween</code>, and<code>whereIn</code>. Every matcher set on a <code>matchOn</code> is combined using logical 'and'. In other words, the following will yield a <code>Predicate</code> that ensures that <code>id</code> equals 1 and <code>email</code> is not null:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var query = new Query() ..matchOn.id = whereEqualTo(1) ..matchOn.email = whereNotNull;</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Matcher expression methods all have a <code>dynamic</code> return type, the actual object type is opaque. The <code>matchOn</code> property it actually an instance of <code>Model</code>, just like other <code>Model</code> objects you may use in your application, but its <code>backingMap</code> has different behavior. This different behavior allows a <code>Query</code> to type check values in matcher expressions, among other things.

Relationship properties may also be matched, but there are important nuances to understand. When matching on <code>RelationshipInverse</code> properties - the properties that represent foreign key columns - you must use the <code>whereRelatedByValue</code> matcher. For example, the following <code>Query</code> would fetch all tasks that belong to <code>User</code> with <code>id</code> equal to 1:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var query = new Query() ..matchOn.user = whereRelatedByValue(1);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice that a matcher is not assigned to the <code>id</code> of the <code>user</code>, but instead, to the <code>user</code> property itself, even though the generated SQL will ultimately evaluate the 'id' column. This matcher will infer the type and name of the foreign key of the related entity. The argument for this matcher provides the value for an equality comparison on the foreign key column of the related entity. (That is, in this example, 'SELECT _task.id, _task.user<em>id, ... FROM _task where _user</em>id = 1'.)

Note that matching on a <code>RelationshipInverse</code> property does not add any additional information to the results of the query. The rules change slightly for hasOne and hasMany relationships. This topic is covered in a later section.

Setting the <code>predicate</code> and using <code>matchOn</code> property of a <code>Query</code> at the same time has undefined behavior, you should only use one or the other. The<code>Predicate</code> generated by a <code>matchOn</code> property is database-agnostic.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="inclidingrelatedobjects">8 Including Related Objects in a Fetch (aka, Joins)</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
A <code>Query</code> can fetch objects that include instances of their hasMany or hasOne relationships alongside their attribute properties. This allows queries to fetch entire model graphs and reduces the number of round-trip queries to a database. (This type of fetch will execute a left outer SQL JOIN.)

To include related objects, you set the <code>includeInResultSet</code> property to <code>true</code> on <code>Query</code>'s <code>matchOn</code>. The returned instances will be instances of the <code>Query</code>'s entity type. For each fetched relationship, these instances' relationship properties will contain instances of their related entities.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User {} class _User { @primaryKey int id;
OrderedSet tasks; ... }
class Task extends Model implements _Task {} class _Task { @primaryKey int id;
@RelationshipInverse(#tasks) User user; ... }
var q = new Query() ..matchOn.id = whereEqualTo(2) ..matchOn.tasks.includeInResultSet = true;
var user = await q.fetchOne();
user.id == 2; user.tasks.every((Task t) =&gt; t.id == 1 &amp;&amp; t.user.id == 2 &amp;&amp; t.text is String ) == true; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
As shown, you may still apply matcher expressions to the primary entity of the query. You may also apply matchers to the related objects. In the case of hasOne relationships, this doesn't make much sense - one you've included the only possible related object, filtering do anything useful. Thus, when fetching hasOne properties, you need only set the relationships <code>includeInResultSet</code> property.

However, in the case of hasMany, it often makes sense to further filter the result set - e.g. fetching a user and their pending tasks, instead of a user and all their entire task history. <code>OrderedSet</code>s - the type of hasMany relationship properties - <em>also</em> has a <code>matchOn</code> property. Matcher expressions applied to the nested <code>matchOn</code> will filter the instances returned in the relationship property.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var q = new Query() ..matchOn.id = whereEqualTo(2) ..matchOn.tasks.includeInResultSet = true ..matchOn.tasks.matchOn.status = whereIn([Status.Pending, Status.RecentlyCompleted]);
var user = await q.fetchOne();
user.id == 2; user.tasks.every((Task t) =&gt; t.id == 1 &amp;&amp; t.user.id == 2 &amp;&amp; (t.status == Status.Pending || t.status == Status.RecentlyCompleted) ) == true;```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
There are two important things to note here. First, if <code>includeInResultSet</code> is false (the default value), the nested <code>matchOn</code> will have no impact on the query (and no instances will be returned for the relationship; adding a matcher expression does not change this property's behavior).

Second, it is vitally important to understand how nested matcher expressions impact the objects returned. In this previous example, the primary entity of the <code>Query</code> - <code>User</code> - has been filtered to only include one user with <code>id</code> equal to <code>2</code>. Thus, the matcher expression on <code>tasks</code> will only be applied to the tasks related to that user. That is, if the user had ten tasks total, and two of them met this condition, the underlying database would fetch ten task rows and remove eight, returning just the two matching rows.

If the <code>Users</code>' <code>id</code> matcher expression was removed, every single user and every single one of their tasks that meets the condition would be fetched. This operation, depending on how many users your application had, could be a very expensive query for the underlying database:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var q = new Query() ..matchOn.tasks.includeInResultSet = true ..matchOn.tasks.matchOn.status = whereIn([Status.Pending, Status.RecentlyCompleted]);
var usersAndTheirTasks = await q.fetch(); // Hang on, this will take awhile! ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
You may fetch multiple relationship properties on the same entity, and you may fetch nested relationship properties as well. This is perfectly valid:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var q = new Query() ..matchOn.id = whereEqualTo(2) ..matchOn.notes.includeInResultSet = true ..matchOn.tasks.includeInResultSet = true ..matchOn.tasks.matchOn.status = whereIn([Status.Pending, Status.RecentlyCompleted]) ..matchOn.tasks.matchOn.locations.includeInResultSet = true;</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
This query would return a single <code>User</code> instance, for which it would have instances of <code>Note</code>s (for <code>notes</code> property), <code>Task</code>s (in <code>tasks</code>), and every <code>Task</code> in <code>tasks</code>would have instances of <code>Location</code> in their <code>locations</code> property. Each of these could have additional matcher expressions to further filter the result set. Also note that in this example, the <code>locations</code> of <code>tasks</code> would already be filtered to only refer to tasks that were <code>Status.Pending</code> or<code>Status.RecentlyCompleted</code> <em>and</em> belong to user with <code>id</code> equal to <code>2</code>.

While <code>RelationshipInverse</code> properties cannot be included using <code>includeInResultSet</code>, the functionality is possible by changing the <code>Query</code>'s entity type to the type of the related inverse property:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart // DO NOT includeInResultSet for RelationshipInverse properties: var q = new Query() ..matchOn.user = whereRelatedByValue(1) ..matchOn.user.includeInResultSet = true;
var tasks = await q.fetch(); // This will not include any information about the user other than its id.
// DO go a level higher in the entity hierarchy and filter by the primary key var q = new Query() ..matchOn.id = 1 ..matchOn.tasks.includeInResultSet = true;
var tasks = await q.fetchOne(); // This will return a user and all tasks in the tasks property. ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
By default, the <code>defaultProperties</code> of the included nested relationship objects are fetched. You may set the fetched properties for the instances fetched in a relationship property with a <code>Query</code>'s <code>nestedResultProperties</code>. This <code>Map&lt;Type, List&gt;</code> uses the instance type of the nested object as a key; the value is a <code>List</code> of properties to fetched on the entities of the keyed type.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var q = new Query() ..nestedResultProperties[Task] = ["id", "text"] ..matchOn.id = 1 ..matchOn.tasks.includeInResultSet = true;</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that a query will always add the primary key of a nested object if it is omitted in <code>nestedResultProperties</code>.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="exceptionserrors">9 Exceptions and Errors</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
An error encountered in preparing or executing a query will throw a <code>QueryException</code>. A <code>QueryException</code> extends <code>HTTPResponseException</code>, and therefore has a suggested HTTP status code. For common scenarios - like a unique violation generating an exception with suggested status code of <code>409</code> - Aqueduct will return a reasonable status code to the requesting HTTP client. Therefore, you do not have to catch query executions unless you wish to override the returned status code.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="statementreuse">10 Statement Reuse</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Aqueduct will parameterize and reuse queries when possible. This allows for significant speed and security improvements. Note that you do not have to do anything special to take advantage of this feature. However, currently at this time, you may not disable this feature.
[/av_textblock]

[/av_three_fourth]
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='#545355' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='center center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#000000' overlay_pattern='' overlay_custom_pattern='']
[av_one_full first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='Get Aqueduct News ' color='custom-color-heading' style='blockquote modern-quote modern-centered' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[/av_one_full][av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][av_one_half min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_codeblock wrapper_element='' wrapper_element_attributes='']
<!--HubSpot Call-to-Action Code -->
<span id="hs-cta-wrapper-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-wrapper">
<span id="hs-cta-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-node hs-cta-38cddba6-7fda-475c-9b91-421388cbf122">
<!-- [if lte IE 8]>
<div id="hs-cta-ie-element"></div>
<![endif]-->
<a href="http://cta-redirect.hubspot.com/cta/redirect/706489/38cddba6-7fda-475c-9b91-421388cbf122"><img id="hs-cta-img-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-img" style="border-width: 0px;" src="https://no-cache.hubspot.com/cta/default/706489/38cddba6-7fda-475c-9b91-421388cbf122.png" alt="GET AQUEDUCT NEWS" /></a>
</span>
<script charset="utf-8" src="https://js.hscta.net/cta/current.js"></script>
<script type="text/javascript">
        hbspt.cta.load(706489, '38cddba6-7fda-475c-9b91-421388cbf122', {});
    </script>
</span>
<!-- end HubSpot Call-to-Action Code -->
[/av_codeblock]

[/av_one_half][av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth]
[/av_section]