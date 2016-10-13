---
ID: 42
post_title: 'Chapter 5: Deploying and Other Fun Things'
author: Jorge Vieira
post_date: 2016-08-11 13:18:57
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/tutorials/chapter-5-deploying-and-other-fun-things/
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
[av_heading heading='Deploying and Other Fun Things' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='subheading_above' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 5
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Tutorial 5']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
This chapter expands on the <a href="/tutorials/chapter-4-model-relationships-and-joins/">previous</a>.

We’ve only touched on a small part of <code class="highlighter-rouge">aqueduct</code>, but we’ve hit the fundamentals pretty well. The rest of the documentation should lead you towards more specific features, in a less hand-holding way. A lot of the code you have written throughout the tutorial is stuff that exists in the <a href="https://github.com/stablekernel/wildfire">wildfire</a> template-generating package. So it’s likely that this is the last time you’ll write the ‘setup code’ you wrote throughout this tutorial.

Make sure you use and check out the instructions on the <code class="highlighter-rouge">wildfire</code> page when you start building your next project - it has helpful tools for everything we will discuss, takes care of boilerplate, and adds a helper for setting up tests in one line of code.

There is one last thing we want to cover, though, and that is deployment.

We’re not going to advocate a specific tool or process for deployment, but we can show you how <code class="highlighter-rouge">aqueduct</code> helps. First, we need to get <code class="highlighter-rouge">quiz</code>’s schema onto a real database. The following Dart script, available in <code class="highlighter-rouge">wildfire</code>, will generate a list of PostgreSQL commands to create the appropriate tables, indices and constraints on a PostgreSQL database. You can drop this in your <code class="highlighter-rouge">bin</code> directory and name it <code class="highlighter-rouge">generate_schema.dart</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">import 'package:quiz/quiz.dart';
import 'dart:io';

main() {
  var dataModel = new DataModel([Question, Answer]);
  var persistentStore = new PostgreSQLPersistentStore(() =&gt; null);
  var ctx = new ModelContext(dataModel, persistentStore);

  var generator = new SchemaGenerator(ctx.dataModel);
  var json = generator.serialized;
  var pGenerator = new PostgreSQLSchemaGenerator(json);

  var schemaFile = new File("schema.sql");
  schemaFile.writeAsStringSync(pGenerator.commandList);
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Running that script from the top-level directory of <code class="highlighter-rouge">quiz</code> like this:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dart bin/generate_schema.dart</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
will create a file named <code class="highlighter-rouge">schema.sql</code>. You can add that to a database via the command-line tool for PostgreSQL:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">psql -h  -p  -U  -d  -f schema.sql</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
(We’re currently working on database migration tools, so if you are already thinking about ‘OK, but what if I change this?’ We’re on it.) Next, we need to allow our <code class="highlighter-rouge">quiz</code> app to take database connection info from a configuration file. For that, we need the <code class="highlighter-rouge">safe_config</code> package (also by <code class="highlighter-rouge">stable|kernel</code>). Add it to <code class="highlighter-rouge">pubspec.yaml</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">name: quiz
description: A quiz web server
version: 0.0.1
author: Me

environment:
  sdk: '&gt;=1.0.0 &lt;2.0.0' dependencies: aqueduct: any safe_config: any dev_dependencies: test: '&gt;=0.12.0 &lt;0.13.0'</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Then run <code class="highlighter-rouge">pub get</code>. The <code class="highlighter-rouge">safe_config</code> package allows you to create subclasses of <code class="highlighter-rouge">ConfigurationItem</code> that match keys in a config file to prevent you from naming keys incorrectly and enforcing required configuration parameters. There is a built-in class in <code class="highlighter-rouge">safe_config</code> specifically for database connections, <code class="highlighter-rouge">DatabaseConnectionConfiguration</code> that we will use. In <code class="highlighter-rouge">pipeline.dart</code>, declare a new class at the bottom of the file that represents all of the configuration values you will have in <code class="highlighter-rouge">quiz</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class QuizConfiguration extends ConfigurationItem {
  QuizConfiguration(String fileName) : super.fromFile(fileName);

  DatabaseConnectionConfiguration database;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Next, we will create a ‘configuration source file’. This file gets checked into source control and is a template for environment-specific configuration files. On a particular instance, you will duplicate this configuration source file and change its values to the appropriate settings for the environment. In the top-level <code class="highlighter-rouge">quiz</code> directory, create <code class="highlighter-rouge">config.yaml.src</code> and add the following:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">database:
 username: dart
 password: dart
 host: localhost
 port: 5432
 databaseName: dart_test</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, we need to do two things to make this configuration file become a reality. First, whenever we start the <code class="highlighter-rouge">Application</code>, we need to pass this configuration information to each pipeline. Then, the pipeline must use this information to tell the persistent store of its <code class="highlighter-rouge">ModelContext</code> where to connect to. (Right now, we hardcoded it to our local database for testing.)

Let’s take care of the pipeline stuff first. Add a new static property in <code class="highlighter-rouge">pipeline.dart</code> and update the constructor:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class QuizPipeline extends ApplicationPipeline {

  static String ConfigurationKey = "QuizPipeline.Configuration";

  QuizPipeline(Map options) : super(options) {
    var dataModel = new DataModel([Question, Answer]);

    var config = options[ConfigurationKey];

    var db = config.database;
    var persistentStore = new PostgreSQLPersistentStore.fromConnectionInfo(db.username, db.password, db.host, db.port, db.databaseName);
    context = new ModelContext(dataModel, persistentStore);
  }

  ...</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Next, we’ll need to read in the configuration file as an instance of <code class="highlighter-rouge">QuizConfiguration</code> and pass it to the startup options of an application. An application will automatically forward this configuration object on to pipelines in their <code class="highlighter-rouge">options</code> - which we utilize in the code we just wrote. First, let’s do this in our tests. Near the top of main function in <code class="highlighter-rouge">question_controller_test.dart</code>, add configuration parameters to the <code class="highlighter-rouge">app</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">void main() {
  var app = new Application();
  var client = new TestClient(app.configuration.port);

  var config = new QuizConfiguration("config.yaml.src");
  app.configuration.pipelineOptions = {
    QuizPipeline.ConfigurationKey : config
  };

  setUpAll(() async {
    ...</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice here that we load the configuration values from the configuration <em>source</em> file. So, the source file serves two roles: it is the template for real instances of your web server, but it also holds the configuration values for testing. This is by convention, and it works itself out really well. Run your tests again - because the configuration source file has the same database connection parameters as your local test database, your tests will still run and pass.

Now, you’ll need to update the the <code class="highlighter-rouge">bin/quiz.dart</code> script that runs the server to also read in a real configuration file.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">import 'package:quiz/quiz.dart';

void main() {
  var config = new QuizConfiguration("config.yaml");
  var app = new Application()
    ..configuration.pipelineOptions = {
      QuizPipeline.ConfigurationKey : config
    };

  app.start();
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
To get your code onto a server, we recommend putting it in a GitHub repository, setting up an <a href="https://help.github.com/articles/generating-an-ssh-key/">access key</a>, and then cloning the repository onto your remote machine. Then, copy the configuration source file into a file named <code class="highlighter-rouge">config.yaml</code> (the one being referenced from <code class="highlighter-rouge">bin/quiz.dart</code>) with values pointing at your actual database. The database you are running won’t have questions or answers, so if you wish to one-time seed the database, the following SQL will work (after creating the tables):
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">into _question (description) values ('How much wood would a woodchuck chuck?');
insert into _question (description) values ('What is the tallest mountain?');
insert into _answer (description, question_index) values ('Depends on if it can.', 1);
insert into _answer (description, question_index) values ('Mount Everest.', 2);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
(Of course, there are much better ways of doing that than typing it out yourself, but that’s a whole other topic.)

Finally, to run your application, you simply run the following command from the top-level of <code class="highlighter-rouge">quiz</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">nohup dart bin/start.dart &gt; /dev/null 2&gt;&amp;1 &amp;</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
If you want to take down the server, you can run the kill command on the process. If you’re running this on a server, you can just use the following command:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">pkill dart</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
However, if you are running it locally, don’t use the trailing <code class="highlighter-rouge">&amp;</code>, that way you can simply cancel the process from your command line with Ctrl-C.

Lastly, remember, you’ll have to install Dart on your target machine.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="documentation">5.1 Documentation</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
<code class="highlighter-rouge">aqueduct</code> has a built-in Swagger spec documentation generator feature. Check out the <a href="https://github.com/stablekernel/wildfire">wildfire</a> repository for the <code class="highlighter-rouge">bin/generate_api_docs.dart</code> script.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="automated-testing">5.2 Automated Testing/CI</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Again, <code class="highlighter-rouge">wildfire</code> is your best bet here as this already exists in projects created with it. However, if you want to add support for running <code class="highlighter-rouge">aqueduct</code> tests as part of Travis-CI, the following .travis.yml file will do:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">language: dart
sudo: required
addons:
  postgresql: "9.4"
services:
  - postgresql
before_script:
  - psql -c 'create database dart_test;' -U postgres
  - psql -c 'create user dart with createdb;' -U postgres
  - psql -c "alter user dart with password 'dart';" -U postgres
  - psql -c 'grant all on database dart_test to dart;' -U postgres
  - pub get
script: pub run test -j 1 -r expanded</code></pre>
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="logging">5.3 Logging</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
<code class="highlighter-rouge">aqueduct</code> logs requests, the amount of information depending on the result of the request. These are logged at the ‘info’ level using the <code class="highlighter-rouge">logger</code> package. At more granular levels, <code class="highlighter-rouge">aqueduct</code> also logs database queries. <code class="highlighter-rouge">wildfire</code> templates incorporate the <code class="highlighter-rouge">scribe</code> package to manage logging to files and the console. See it for more examples.
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