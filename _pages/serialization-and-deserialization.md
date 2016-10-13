---
ID: 155
post_title: Serialization and Deserialization
author: Jorge Vieira
post_date: 2016-10-04 11:42:54
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/documentation/serialization-and-deserialization/
published: true
sidebar:
  - 'a:1:{i:0;s:0:"";}'
footer:
  - 'a:1:{i:0;s:0:"";}'
header_title_bar:
  - 'a:1:{i:0;s:0:"";}'
header_transparency:
  - 'a:1:{i:0;s:0:"";}'
---
[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='top center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#545355' overlay_pattern='' overlay_custom_pattern='']

[av_heading heading='Serialization and Deserialization' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 5
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']

[/av_section][av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Doc - Serialization and Deserialization']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
While model objects are responsible for representing database rows, they are also responsible for serializing and deserializing data. Serialization converts a model object to a <code>Map&lt;String, dynamic&gt;</code>where each property on the model object is a key-value pair in the map. Each key is the exact name of the property. This is done by the <code>asMap</code> method.

Deserialization ingests key-value pairs from a <code>Map&lt;String, dynamic&gt;</code> and assigns it to the properties of a model object, where each value in the map is assigned to the property whose key matches exactly the name of the property. This is done by the <code>readMap</code> method. The following code demonstrates this behavior:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var userMap = { "id" : 1, "name" : "Bob" };
var user = new User()..readMap(userMap);
var outUserMap = user.asMap();
// userMap == outUserMap ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that serialization and deserialization are encoding agnostic. Data typically enters an application as JSON in an HTTP request body. From there, it is decoded into Dart objects like <code>Map</code>,<code>String</code> and <code>List</code>. It is only once the data is in this format that it can be deserialized into a model object. Thus, model objects don't understand what an intermediary format like JSON is. Likewise, serializing a model object produces data that can be encoded into JSON, but it does not create the JSON itself. It is up to the mechanism that is generating the HTTP response to determine the encoding format; thus, it is possible to encode model objects into another format like protocol buffers if the application calls for it.

When serializing and deserializing a model object, it is important to understand the nuances of the<code>null</code> value. As indicated earlier, a <code>Model</code> object is a glorified <code>Map</code>. When a row is fetched from a database and decoded into a <code>Model</code> object, every column/value pair is set in a <code>Model</code>'s<code>backingMap</code>. If a property is not fetched from the database, its key is not present in the backing map. (Likewise, if you create an instance of <code>Model</code>, its backing map contains no key-value pairs and only contains those that you explicitly set.) Therefore, when accessing the property of a <code>Model</code>object that was not previously set, you will get the value <code>null</code>.

However, it is also possible that a property's value is actually the <code>null</code> value and it is important to understand how this distinction impacts serialization. During serialization, if a key is not present in the backing of a <code>Model</code>, it is omitted from the serialized object. If the value of a property has been explicitly set to <code>null</code>, the key will be present and the value will be <code>null</code>. Therefore, consider the following two scenarios:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User()..id = 2; var map = user.asMap(); map == { 'id' : 2 };
user.name = null; map = user.asMap(); map = { 'id' : 2, 'name' : null }; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The inverse is true when deserializing: any keys not present in the <code>Map</code> will not be set in the model's backing.

If you ever need to check whether or not a value has been set on a <code>Model</code>, you may access use the method <code>hasValueForProperty</code> or access its <code>backingMap</code> directly:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User()..id = 2;
user.hasValueForProperty("id"); // true user.backingMap.containsKeys("id"); // true
user.backingMap.containsKeys("name"); // false user.hasValueForProperty("name"); // false ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Because setting the value <code>null</code> for a property doesn't "remove" that property from the backing map, you may explicitly remove a property from the backing using the method<code>removePropertyFromBackingMap</code> on <code>Model</code>.</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User() ..id = 2 ..name = 'Bob'; var map = user.asMap(); map == { 'id' : 2, 'name' : 'Bob' };
user.name = null; map = user.asMap(); map == { 'id' : 2, 'name' : null };
user.removePropertyFromBackingMap("name"); map = user.asMap(); map == { 'id' : 2 }; ```</code></pre>
[/av_textblock]

[av_heading heading='<span id="transientproperties">1 Transient Properties and Serialization/Deserialization</span>' tag='h2' style='blockquote modern-quote' size='30' subheading_active='' subheading_size='15' padding='10' color='' custom_font='#ffffff'][/av_heading]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">By default, transient properties and getters are <em>not</em> included in the <code>Map</code> produced when serializing a model object. (Setters are obviously not included, as they don't hold a value.) To include a transient property or getter during serialization, you may mark it with <code>@transientOutputAttribute</code>metadata. Properties marked with this metadata will be included in the serialized <code>Map</code> if and only if they are not null. A good reason to use this feature is when you want to provide a value to the consumer of the API that is derived from one or more values in persistent type of the model object:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User { @transientOutputAttribute String get fullName =&gt; "$firstName $lastName"; }
class _User { String firstName; String lastName;
... }
var user = new User() ..firstName = "Bob" ..lastName = "Boberson";
var map = user.asMap(); map == { 'firstName' : 'Bob', 'lastName' : 'Boberson', 'fullName' : 'Bob Boberson' };
```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Transient properties may also be used as inputs when deserializing a <code>Map</code> into a model object by marking the property with <code>@transientInputAttribute</code>. For example, consider how to handle user passwords. The persistent type - a direct mapping to the database - does not have a password property for security purposes. Instead, it has a password hash and a salt. An instance type could then define a password property, which automatically set the salt and hash of the password in the underlying persistent type:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User { @transientInputAttribute void set password(String pw) { salt = generateSalt(); hashedPassword = hash(pw, salt); } } class _User { String salt; String hashedPassword; ... }
var map = { 'password' : 'mypassword' }; var user = new User()..readMap(map); // also equivalent to user.password = 'mypassword'; var salt = user.salt; // 'somerandomstring' var hashedPassword = user.hashedPassword; // 'somehashedstring'
var password = user.password; // error, this property does not exist! ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Transient inputs must be setters or properties. For properties that are both inputs and outputs, you may use the metadata <code>@transientAttribute</code>. Also, a separate getter and setter may exist for the same name to allow both input and output:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart class User extends Model implements _User { @transientInputAttribute void set transientValue(String s) { ... }
@transientOutputAttribute String get transientValue =&gt; ...; } ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Transient properties marked with this metadata <em>are</em> <code>attributes</code> on an entity (like primitive properties on the persistent type, but unlike other properties on the instance type).</span>
[/av_textblock]

[av_heading heading='<span id="relationships">2 Serialization and Deserialization of Relationships</span>' tag='h2' style='blockquote modern-quote' size='30' subheading_active='' subheading_size='15' padding='10' color='' custom_font='#ffffff'][/av_heading]

[av_textblock size='' font_color='' color='']
A model object will serialize any relationship properties as <code>Map</code>s or a <code>List&lt;Map&gt;</code> if those properties are present in its <code>backingMap</code>.

'hasOne' and inverse properties are always serialized as <code>Map</code>s. Thus, the following:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User() ..id = 1; ..profile = (new Profile()..id = 2);
var userMap = user.asMap(); userMap == { 'id' : 1, 'profile' : { 'id' : 2 } }; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">'hasMany' relationships are always serialized as a <code>List</code> of <code>Map</code>s:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var user = new User() ..id = 1; ..posts = new OrderedSet.from([ new Post()..id = 2, new Post()..id = 3 ]);
var userMap = user.asMap(); userMap == { 'id' : 1, 'posts' : [ { 'id' : 2 }, { 'id' : 3 } ] }; ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">It is important to note the potential for cyclic object graphs. Since all relationship properties have an inverse, the two properties in that relationship are references to one another. That is, you could do something like this:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart identical(user.profile.user, user); identical(user.posts.first.user, user);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">When fetching objects from a database, this won't happen - Aqueduct will create multiple instances of the same row when necessary to avoid this. Therefore, the previous code snippet would not be true, but the following two statements would be:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">``` user.profile.user.id == user.id;
user.posts.first.user.id == user.id ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">While fetched objects will not have cyclic references, model objects you instantiate yourself can (if you mistakenly do so). While these cyclic object graphs can be used build <code>Query</code>s, they cannot be serialized. You'll get a stack overflow error. It's best to avoid creating cyclic graphs altogether. For example:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart
// do: var user = ... from somewhere ... posts.forEach((p) { p.user = new User()..id = user.id; });
// do not: var user = ... from somewhere ... posts.forEach((p) { p.user = user; }); ```</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Relationships may also be deserialized from a map or list of maps. Thus, the following will do what you expect:</span>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">```dart var map = { 'id' : 1, 'name' : 'Bob' 'profile' : { 'id' : 3, 'profilePhotoURL' : 'http://somewhereout.there' }, 'posts' : [ { 'id' : 2, 'text' : 'Foo' } ] };
var user = new User() ..readMap(map); ```</code></pre>
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