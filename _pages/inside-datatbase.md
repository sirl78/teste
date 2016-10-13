---
ID: 157
post_title: Inside Datatbase
author: Jorge Vieira
post_date: 2016-10-04 11:43:20
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/documentation/inside-datatbase/
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
[av_heading heading='The Layers Between Aqueduct and Your Database' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 2
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Doc - Inside Datatbase']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Aqueduct applications use a number of objects to facilitate integrating with a database. Your application code will create instances of <code>Query</code> that get executed against a <code>ModelContext</code>. A <code>ModelContext</code> uses an instance of a <code>PersistentStore</code> to map queries to a specific database flavor. The data returned from a database is then mapped into <code>Model</code> objects by the <code>ModelContext</code>. The context is able to performing this mapping with its instance of <code>DataModel</code>, which contains<code>ModelEntity</code>s that represent the model objects in your application.</span>
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="modelcontext">1 ModelContext is the Bridge from Aqueduct to a Database</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
An instance of a <code>ModelContext</code> is necessary for interaction with a database. It is the interface between your application code and a database. When you execute a <code>Query</code>, that query is executed on a specific instance of <code>ModelContext</code>. A <code>ModelContext</code> will take the results of a <code>Query</code> and map them back to model objects. Most applications will only have one <code>ModelContext</code>. (Applications that talk to more than one database or different schemas within a database will have more.) A<code>ModelContext</code> is responsible for using a <code>PersistentStore</code> and <code>DataModel</code> to translate <code>Model</code> object to and from database rows and <code>Query</code> objects to and from SQL.

Because most applications only have one <code>ModelContext</code>, there is a default context for every application. If you are only creating a single <code>ModelContext</code> in an application, that context is set to be the default context without any further action. By default, a new <code>Query</code> will run on the default context of an application. The default context can be changed, but this is rarely done:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart ModelContext.defaultContext = new ModelContext(dataModel, persistentStore);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<span class="Apple-style-span">Contexts are typically instantiated in a <code>ApplicationPipeline</code>'s constructor or some other point in an application's startup process. Contexts are rarely accessed directly after they are created. A<code>Query</code>, when executed, will work with private methods on a context to carry out its job. A context must be instantiated with a <code>DataModel</code> and <code>PersistentStore</code>, and the context effectively coordinates these two objects to carry out its tasks.</span>
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="datamodels">2 DataModels Describe an Application’s Entities</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Instances of <code>DataModel</code> are one of the two components of a <code>ModelContext</code>. A <code>DataModel</code> has a definition for all of the model objects that can be interacted with in a particular context. In most applications, this means every model/persistent type pair you declare in your application.<code>DataModels</code> are instantiated with a <code>List</code> of instance types. The <code>DataModel</code> will create instances of<code>ModelEntity</code> to describe each instance type it is given. In other words, a <code>DataModel</code> compiles your model class declarations into entities that contain information at runtime to map data back and forth between a database.

<code>ModelEntity</code>s are the description of a database table in your application. A <code>ModelEntity</code> contains references to the two types that make up an fully formed entity. They also contain the information derived from these types - the attributes and relationships - into a more readily available format.

<code>ModelEntity</code>s store relationship and attribute information in instances of <code>RelationshipDescription</code>and <code>AttributeDescription</code>, both of which extend <code>PropertyDescription</code>. This information is used by the rest of Aqueduct to determine how database rows and model objects are translated back and forth. This information is derived from persistent and instance type declarations, and<code>AttributeHint</code>s and <code>RelationshipInverse</code> metadata that is used when defining your model classes.

A <code>DataModel</code> will also validate all entities and their relationships. If validation fails, an exception will be thrown. As <code>DataModel</code>s are created at the beginning of the application's startup, this behavior will stop your application from running if there are data model errors.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="persistentstores">3 Persistent Stores Handle Database Queries</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
<code>Query</code>s created in an Aqueduct application are database-agnostic. They are defined in the domain of your <code>DataModel</code> and its <code>ModelEntity</code>s. A <code>PersistentStore</code> is responsible for translating a <code>Query</code>into a specific flavor of SQL and execute that query against a remote database. A <code>ModelContext</code>uses a <code>PersistentStore</code> to carry out data transmission.

<code>PersistentStore</code> is an abstract class. To connect to and interact with a specific flavor of SQL - like PostgreSQL or MySQL - a flavor-specific implementation of <code>PersistentStore</code> must exist. By default, Aqueduct ships with a <code>PostgreSQLPersistentStore</code>. A persistent store implementation does the actual translation from Aqueduct <code>Query</code>s to a SQL query. It also manages a database connection and the transmission of data between your application and a database instance.

There is nothing that prevents a <code>PersistentStore</code> implementation from connecting to and working with a NoSQL database, but the interface is certainly more geared towards SQL databases.

<code>PersistentStore</code>s are rarely used directly. Instead, a <code>ModelContext</code> has a persistent store that it uses to coordinate database queries. Prior to sending a <code>Query</code> to a persistent store, a<code>ModelContext</code> will transform a <code>Query</code> into a <code>PersistentStoreQuery</code>. <code>PersistentStoreQuery</code>s are effectively the 'compiled' version of a <code>Query</code>.

<code>PersistentStore</code>s may be used directly to issue direct SQL to its underlying database connection. This is often useful for scripts and tests that modify a database schema. For this purpose,<code>PersistentStore</code> has an <code>execute</code> method to run raw SQL.
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