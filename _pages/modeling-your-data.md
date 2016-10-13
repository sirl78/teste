---
ID: 161
post_title: Modeling Your Data
author: Jorge Vieira
post_date: 2016-10-04 11:44:01
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/documentation/modeling-your-data/
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
[av_heading heading='Modeling Your Data' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 5
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Doc - Modeling Your Data']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
In Aqueduct, data from a database is represented by <em>model objects</em>. A model object is an instance of a <code>Model</code> subclass that you define in your application code. These subclasses are used to map a Dart object to a database row. Thus, every instance of a <code>Model</code> object in your application represents a row (or a row to be inserted) in a database. These subclasses are referred to as<em>entities</em>. For example, you may have an application with a <code>User</code> class that is a subclass of <code>Model</code>. This creates a User entity in your application, which maps to a user table in your database. When fetching rows from the user table, your application will receive instances of <code>User</code> that represent these rows.

An entity must have properties that correspond to the columns in the table it represents. An entity is actually made up of two classes: a <em>persistent type</em> and an <em>instance type</em>. The persistent type is a plain Dart class that defines the mapping to a database table and its columns. A instance type is the subclass of <code>Model</code>; instances of this type are used in an application.

Here's an example of a model and persistent type, which are always declared together:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User {
}
class _User { @primaryKey int id;
String name; } ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
This declares a User entity. The instance type here is <code>User</code> because it extends <code>Model</code>. The <code>Model</code>class provides storage for an object that is fetched from a database. It also handles serialization and deserialization so that a model object can be encoded or decoded into formats like JSON.

The type parameter for a <code>Model</code> must be the entity's corresponding persistent type. Here, the persistent type is <code>_User</code>. (By convention, a persistent type is prefixed with an underscore.) The persistent type declares properties for every column that is actually on the database table this entity maps to. In this example, we are declaring that there is a table named *_User* and it has two columns, an integer primary key named <code>id</code> and a text column named <code>name</code>.

A instance type must also implement the interface of its persistent type. This allows the instance type to have accessor methods for each of the properties defined in its persistent type:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User();
user.id = 1; user.name = "Bob"; ```
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Because the instance type implements the persistent type (as opposed to extending it), the inherited properties from the persistent type do not have instance storage. That is, the instance type only exposes a getter and setter for each property in the persistent type, but does not have an instance variable to store any values. Instead, the instance type's superclass, <code>Model</code>, takes care of storage by overriding <code>noSuchMethod</code> to store and retrieve values from its <code>backingMap</code>. By providing the type parameter <code>_User</code> to the <code>Model</code> superclass, the <code>User</code> can validate properties against the interface provided in <code>_User</code>.

Thus, a persistent type is never instantiated, it simply provides the mapping information between your model objects and a database table. All entities must be declared in this two-class setup. In the long-run, this significantly cuts down on typing and properly differentiates between database-backed properties and functionality that a model object may expose on top of those values.

In order to use an entity in your application, it must be compiled into a <code>DataModel</code> [see inside<em>the</em>db.md]. A <code>DataModel</code> will create instances of <code>ModelEntity</code> that preprocess and validate the information described in your persistent and instance types at application startup.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="persistenttypes">1 More on Persistent Types</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Persistent types define the mapping between your code and a database table (and are often used to generate those tables in a database). As each property in a persistent type represents a database column, the type of the property must be storable in a database. The following types are available as primitive properties on a persistent type:
<ul>
 	<li>int</li>
 	<li>double</li>
 	<li>String</li>
 	<li>DateTime</li>
 	<li>bool</li>
</ul>
Properties that are one of these types are more specifically referred to the <em>attributes</em> of an entity. (Properties that are references to other model objects are called <em>relationships</em>. Collectively, attributes and relationships are called properties.)

In addition to a type and name, each property can also have <code>AttributeHint</code> that further specifies the corresponding column. <code>AttributeHint</code> is added as metadata to a property. For example, the following change to the <code>_User</code> persistent type adds a <code>String</code> <code>email</code> property which must be unique across all users:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class _User { @primaryKey int id; String name;
@AttributeHint(unique: true) String email; } ```
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
There are eight configurable hints available in the<code>AttributeHint</code> class.
<ul>
 	<li><code>primaryKey</code> - Indicates that property is the primary key of the table represented by this persistent type. Must be one per persistent type.</li>
 	<li><code>databaseType</code> - Uses a more specific type for the database column than can be derived from the Dart type of the property. For example, you may wish to specify that an integer property is stored in a database column that holds an 8-byte integer, instead of the default 4-byte integer.</li>
 	<li><code>nullable</code> - Toggles whether or not this property can contain the null value.</li>
 	<li><code>defaultValue</code> - A default value for this property when inserted into a database without an explicit value.</li>
 	<li><code>unique</code> - Toggles whether or not this property must be unique across all instances of this type.</li>
 	<li><code>indexed</code> - Toggles whether or not this property's database column should be indexed for faster searching.</li>
 	<li><code>omitByDefault</code> - Toggles whether or not this property should be fetched from the database by default. Useful for properties like hashed passwords, where you don't want to return that information when fetching an account unless you explicitly want the check the password.</li>
 	<li><code>autoincrement</code> - Toggles whether or not the underlying database should generate a new value from a serial generator each time a new instance is inserted into the database.</li>
</ul>
By not specifying <code>AttributeHint</code>, the default values for each of these possible configurations is used and the database type is inferred from the type of the property.

Every persistent type must have at least one property with <code>AttributeHint</code> where <code>primaryKey</code> is true. There is a convenience instance of <code>AttributeHint</code> for this purpose, <code>@primaryKey</code>, which is equivalent to the following:

<code>dart @AttributeHint(primaryKey: true, databaseType: PropertyType.bigInteger, autoincrement: true)</code>By convention, persistent types begin with an underscore, but there is nothing that prevents you from changing this. Bear in mind, the name of the persistent type will be the name of the corresponding database table (and some databases, like PostgreSQL, already have a table named 'user'). You may override the name of the table by implementing a static method that returns the name of the table in a persistent type:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class _User { @primaryKey int id; String name;
static String tableName() { return "UserTable"; } } ```
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Note that the specific database driver determines whether or not the table name is case-sensitive or not. The included database driver for PostgreSQL automatically lowercases table names and is case-insensitive.</span>
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="instancetypes">2 Instance Types</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span"> In using model objects in your application code, you will always use instances of an entity's instance type. Model objects can be transferred to and from a database to insert or fetch data. Model objects can also read their properties from a <code>Map</code>, oftentimes from JSON data in an HTTP request body. Model objects also know how to serialize themselves back into a <code>Map</code>, so they can be used as an HTTP response body. Additionally, model objects are used to help build queries in a safe way. The following code snippet is a pretty common usage of a model object: </span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart @httpPost createThing() async { // Construct User from HTTP request body JSON var userFromRequestBody = new User() ..readMap(requestBody);
// Construct Query for inserting the user, using values from the request body. var insertQuery = new Query() ..values = userFromRequestBody;
// Execute insert, get User back from database var insertedUser = await insertQuery.insert();
// Return response with inserted User serialized as HTTP response body. return new Response.ok(insertedUser); } ```
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
When getting model objects from a database, each instance will represent one row. For example, consider the following table, and the previous example of <code>_User</code> and <code>User</code> persistent and instance types:

id|name --|---- 1|Bob 2|Fred

If this entire table were fetched, you'd get a <code>List</code> as though you had written the following code:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart var users = [ new User() ..id = 1 ..name = "Bob", new User() ..id = 2 ..name = "Fred" ];</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
instance types may also define properties and methods on top of those it implements from its persistent type. Because these properties and methods are not part of the persistent type, they are <em>transient</em> - that is, they are not stored in the database. Any method or property defined on an instance type is ignored when used in a <code>Query</code>. This is in contrast to a persistent type, where every property explicitly maps to a database column.

It is often the case that you have a method or property on the instance type that makes some operation more convenient. For example, consider an entity that represented a video on a video sharing site. Each video has a persistent property that indicates when the video was uploaded. As a convenience, you'd like to be able to determine if a video instance is "recent" - that is, it has been uploaded in the last week. Adding a <code>isRecent</code> property to the persistent type doesn't make any sense, because that information can be derived from the existing upload date property. Thus, its a good use of a transient property:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class Video extends Model implements _Video { bool get isRecent =&gt; return new DateTime.now().difference(uploadDate).inDays &lt; 7; }
class _Video { @primaryKey int id; DateTime uploadDate; ... } ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that, by default, transient properties are not serialized or deserialized, and are not<code>attributes</code> of their entity.

It is important to understand that a <code>Model</code> is a effectively a wrapper around a<code>Map&lt;String, dynamic&gt;</code>. This <code>Map</code> is the <em>backing</em> of the <code>Model</code> object. A <code>Model</code> object's values are stored in this <code>Map</code> - when you access a property of a model object, the name of the property is transformed into a <code>String</code> key in the backing map. This is why the instance type <em>implements</em> its persistent type - the actual storage for the properties are in this backing map, inherited from<code>Model</code>. The <code>Model</code> class implements <code>noSuchMethod</code> to set and get data from its backing map when an accessor is invoked on a <code>Model</code> subclass.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="modelingmodel">3 Modeling Model Object Relationships</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
In addition to attributes, model objects may also have properties that are other model objects or collections of other model objects. These types of properties are called <em>relationships</em>. For example, in a social network application, a user may have many posts that they have created. A user, then, should have a property that is a list of posts. This is called a 'hasMany' relationship, because a user can have many posts.

A user might also have an associated profile, so it should also have a property that is an instance of some profile class. This is called a 'hasOne' relationship, because a user can only ever have one profile.

These relationships are declared in the persistent type of a model. In the above examples, a user would look like this:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart class User extends Model implements _User {} class _User { @primaryKey int id; String name; Profile profile; OrderedSet posts; }
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
An <code>OrderedSet</code> is what indicates that the relationship is hasMany. An <code>OrderedSet</code> is a glorified <code>List</code>- it can do everything a <code>List</code> can do - but has some additional behavior to help manage relationships and build queries.

Relationship properties do <em>not</em> map to columns in the database, but actually map to entire rows in some other table in a database. The type of a relationship property must be the instance type of an entity (as opposed to the persistent type).

All relationships of an entity must have a inverse relationship property on the destination entity. If a user has posts, then posts have a user. If a user has a profile, then a profile has a user. Thus, the<code>Post</code> and <code>Profile</code> entities both must have a property that is a <code>User</code>. Inverse relationship properties, like relationship properties, are declared in the entity's persistent type. Thus, the Post and Profile entities would be declared like so:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class Post extends Model implements _Post {} class _Post { @primaryKey int id; String text;
@RelationshipInverse(#posts) User user; }
class Profile extends Model implements _Profile {} class _Profile { @primaryKey int id; String profilePhotoURL;
@RelationshipInverse(#profile) User user; } ```
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code>RelationshipInverse</code> metadata is the special addition here. This accomplishes two things. First, the <code>RelationshipInverse</code> property is actually a column in the database. More specifically, it is a foreign key to the other entity's table. In SQL databases, relationships are maintained through foreign key references. By specifying <code>RelationshipInverse</code>, you get to pick which table has the foreign key.

Additionally, the first argument to <code>RelationshipInverse</code> allows you to pick the relationship property on the other entity that this property is inversely related to. For example, a User entity could potentially have two relationships with posts: posts they've created and posts that have queued for future posting. The Post table must have two foreign key columns to keep track of whether or not a User has already posted it or simply queued it. This <code>Symbol</code> for the property on the other side of the relationship makes this link.

Finally, the type of the inverse property must be the other entity's instance type.

During <code>DataModel</code> compilation to generate <code>ModelEntity</code>s, inverses are checked for integrity by ensuring that the <code>RelationshipInverse</code> symbol and the types of the relationship properties match. If they do not, the <code>DataModel</code> will throw an exception.

<code>RelationshipInverse</code> properties are always indexed; although this may change in the future to be configurable, but it will always be the default. Additionally, inverse properties are unique if the other side is a 'hasOne' relationship. Because the <code>RelationshipInverse</code> property is actually a foreign key column, it may also define some extra configuration parameters: a delete rule and whether and whether or not it is required.

By making the Post entity's <code>user</code> required, we will require that every <code>Post</code> must have a user in order to be inserted into the database. This means that a <code>Post</code> cannot exist without a user (i.e., the foreign key may not be null),
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart class _Post { ... @RelationshipInverse(#posts, required: true) User user; }
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">By changing the Profile's <code>user</code> delete rule to <code>RelationshipDeleteRule.cascade</code>, deleting a <code>User</code> will also delete its <code>Profile</code>:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class _Profile { ... @RelationshipInverse(#profile, deleteRule: RelationshipDeleteRule.cascade) User user; }</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
By default, the delete rule is <code>nullify</code> (it is the least destructive action) and required is <code>false</code>. If you try and set up a relationship where the inverse property is both <code>nullify</code> and <code>required</code>, you will get a <code>DataModel</code> exception: if the foreign key column can't be null and deleting the related object would nullify the foreign key column... well, that wouldn't work.

When receiving model objects after fetching them from a database, there are rules on which relationship properties are fetched. By default, any 'hasOne' or 'hasMany' relationships are <em>not</em>fetched from the database:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query(); var user = await query.fetchOne();
var userBacking = user.backingMap; userBacking == { 'id' : 1, 'name' : 'Bob' }; // does not contain 'profile' or 'posts' ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
In order to fetch these types of relationships, you must explicitly configure a <code>Query</code> to include them. This is because this type of query is more expensive - it causes a SQL join to be performed. This is covered in the <code>Executing Queries</code> chapter. [in executing_queries.md]

The other side of a relationship - the property with <code>RelationshipInverse</code> - will be fetched by default. However, the entire related object is not fetched - only its <code>primaryKey</code> value. The remainder of its properties will not be present in the <code>backingMap</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var query = new Query(); var profile = await query.fetchOne();
var userBacking = profile.user.backingMap; userBacking == { 'id' : 1 }; // does not contain 'name', 'profile' or 'posts' ```</code></pre>
[/av_textblock]

[/av_three_fourth][/av_section][av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='#545355' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='center center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#000000' overlay_pattern='' overlay_custom_pattern='']
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