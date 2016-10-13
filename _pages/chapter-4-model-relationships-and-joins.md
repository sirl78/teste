---
ID: 41
post_title: 'Chapter 4: Model Relationships and Joins'
author: Jorge Vieira
post_date: 2016-08-11 13:18:41
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/tutorials/chapter-4-model-relationships-and-joins/
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
[av_heading heading=' Model Relationships and Joins' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='subheading_above' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 4
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Tutorial 4']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
This chapter expands on the <a href="/tutorials/chapter-3-interacting-with-a-database/">previous</a>.

Model objects can also have relationships to other model objects. There are two types of relationships: to-one and to-many. Let’s add an answer for each <code class="highlighter-rouge">Question</code> in the form of a to-one relationship. First, create a new file <code class="highlighter-rouge">lib/model/answer.dart</code> and define a new model object to represent an answer:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part of quiz;
class _Answer {
@primaryKey
int id;

String description;
}
class Answer extends Model&lt;_Answer&gt; implements _Answer {}
class AnswerQuery extends ModelQuery implements _Answer {}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice we created the persistent type, instance type and went ahead and created a special <code class="highlighter-rouge">ModelQuery</code>. Link this file back to the library in <code class="highlighter-rouge">lib/quiz.dart</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part 'model/answer.dart';</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now that we have a model class that represents both a question and answer, we will set up a relationship between them. It logically makes sense that a ‘question <em>has a</em> answer’, so let’s add that property to <code class="highlighter-rouge">_Question</code>, the persistent type of <code class="highlighter-rouge">Question</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class _Question {
  @primaryKey
  int index;
String description;
@Relationship.hasOne("question")
Answer answer;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
We have declared a new persistent property, and its type is another <code class="highlighter-rouge">Model</code> object - <code class="highlighter-rouge">Answer</code>. The property is marked with <code class="highlighter-rouge">Relationship</code> metadata, indicating each <code class="highlighter-rouge">Question</code> will have <em>one</em> <code class="highlighter-rouge">Answer</code>. The single parameter for a <code class="highlighter-rouge">Relationship.hasOne</code> is called the <em>inverse key</em>. Whenever you declare a relationship, you must declare it in both persistent types that are involved in the relationship. The inverse key indicates the name of the property on the other class that represents this relationship.

So, let’s set that up. In <code class="highlighter-rouge">_Answer</code>, add the inverse:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class _Answer {
  @primaryKey
  int id;
String description;

@Relationship.belongsTo("answer")
Question question;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice that the inverse has a relationship type of <code class="highlighter-rouge">belongsTo</code>. The persistent type that has the <code class="highlighter-rouge">belongsTo</code> side of the relationship will contain the foreign key column. Notice that the exact names of the properties in the related classes are the same as the argument in the <code class="highlighter-rouge">Relationship</code> metadata. <code class="highlighter-rouge">belongsTo</code> relationship allow you to specify a delete rule and whether or not the property is required, i.e., not nullable. By default, all <code class="highlighter-rouge">belongsTo</code> relationships’ delete rules are nullify and are not required - this is the least destructive action. But, in this case, we want the every question to always have an answer and if we delete the question, the answer gets deleted along with it:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class _Answer {
  @primaryKey
  int id;

  String description;

  @Relationship.belongsTo("answer", deleteRule: RelationshipDeleteRule.cascade, required: true)
  Question question;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
There is nothing extra to do for the <code class="highlighter-rouge">hasOne</code> side of the relationship when setting these extra relationship attributes. (By the way, relationship columns are always indexed.) Finally, we need to add <code class="highlighter-rouge">Answer</code> to the <code class="highlighter-rouge">DataModel</code> in <code class="highlighter-rouge">pipeline.dart</code>. (Also by the way, it is easy to write a function that simply reflects on your application to find all <code class="highlighter-rouge">Model</code> types for you so you don’t have to remember to do this, but again, we’re staying manual to understand the concepts.)

Now that we have defined this relationship, we can associate answers with questions and return them in our <code class="highlighter-rouge">/questions</code> endpoint. In <code class="highlighter-rouge">question_controller.dart</code>, let’s update the queries to join on the <code class="highlighter-rouge">Answer</code> table and include the answer in the response JSON. First, for get all questions:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">@httpGet getAllQuestions({String contains: null}) async {
  var questionQuery = new QuestionQuery()
    ..answer = whereAnyMatch;
  if (contains != null) {
    questionQuery.description = whereContains(contains);
  }
  var questions = await questionQuery.fetch();
  return new Response.ok(questions);
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Yeah, that was it. The matcher <code class="highlighter-rouge">whereAnyMatch</code>, when applied to a <code class="highlighter-rouge">hasOne</code> or <code class="highlighter-rouge">hasMany</code> relationship property, will configure a join on the Answers table when fetching the questions. When the resulting model objects comes back, for each question, its <code class="highlighter-rouge">answer</code> property will be populated (if it has an answer). Model objects also know how to recursively serialize themselves, so you’ll get the following JSON when fetching a question that has been joined with its answer:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">{
  "index" : 1,
  "description" : "A question?",
  "answer" : {
      "id" : 1,
      "description" : "An answer"
  }
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Let’s update our tests to ensure this works correctly. If you run your tests now, the two tests that get a list of <code class="highlighter-rouge">Question</code>s will fail because they don’t expect an answer key in the JSON. Now, we don’t really care about the ‘id’ of the answer at all, just its ‘description’. Therefore, when we add to the HTTP body matcher to match the inner ‘answer’ object, it’d be great if we could just ignore it. That’s why there is the <code class="highlighter-rouge">partial</code> matcher. A <code class="highlighter-rouge">partial</code> matcher will match a <code class="highlighter-rouge">Map</code>, but will only verify the values for each key in the partial matcher. Let’s try that out by updating the first test for getting all questions:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions", () async {
    var response = await client.request("/questions").get();
    expect(response, hasResponse(200, everyElement({
        "index" : greaterThanOrEqualTo(0),
        "description" : endsWith("?"),
        "answer" : partial({
          "description" : isString
        })
    })));
    expect(response.decodedBody, hasLength(greaterThan(0)));
  });</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The partial matcher here will just check to see if the ‘answer’ key is a map that contains a <code class="highlighter-rouge">String</code> ‘description’ value. The extraneous ‘id’ key won’t cause a failure. If you run the tests now, this test will still fail - ‘answer’ is null because there are no answers in the database. Let’s insert some in <code class="highlighter-rouge">setUpAll</code> of <code class="highlighter-rouge">question_controller_test.dart</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">var questions = [
  "How much wood can a woodchuck chuck?",
  "What's the tallest mountain in the world?"
];
var answersIterator = [
  "Depends on if they can",
  "Mount Everest"
].iterator;

for (var question in questions) {
  var insertQuery = new QuestionQuery()
    ..values.description = question;
  question = await insertQuery.insert();

  answersIterator.moveNext();
  insertQuery = new AnswerQuery()
    ..values.description = answersIterator.current
    ..values.question = question;
  await insertQuery.insert();
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Notice that we took the result of the question insert - which returns an instance of <code class="highlighter-rouge">Question</code> - and used it as the value of the <code class="highlighter-rouge">AnswerQuery</code>’s <code class="highlighter-rouge">question</code>. This will take the primary key value of the <code class="highlighter-rouge">question</code> and insert it into the foreign key column in the <code class="highlighter-rouge">Answer</code>. Now, it just so happens we have a full <code class="highlighter-rouge">Question</code> object that we just received from the database that we could set to that property. If we did, and instead had just the <code class="highlighter-rouge">index</code> of the <code class="highlighter-rouge">Question</code>, we’d do this instead:.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">insertQuery = new AnswerQuery()
  ..values.description = answersIterator.current
  ..values.question = (new Question()..index = 1);</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, running the tests against, the first one will succeed again. Update the last test that checks a list of questions when sending a ‘contains’ query parameter:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions filtered by contains", () async {
  var response = await client.request("/questions?contains=mountain").get();
  expect(response, hasResponse(200, [{
      "index" : greaterThanOrEqualTo(0),
      "description" : "What's the tallest mountain in the world?",
      "answer" : partial({
        "description" : "Mount Everest"
      })
  }]));
  expect(response.decodedBody, hasLength(1));
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
All tests are back to passing.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="more-on-joins">4.1 More on Joins and Relationships</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
As relationships and joins are a complex topic, you may want to read the corresponding guide on them to get a full understanding of how <code class="highlighter-rouge">aqueduct</code> supports them. However, it is important to note that to-many relationships are also available. For example, if you wanted many answers for a question, you’d declare the relationship property as <code class="highlighter-rouge">hasMany</code> and make it a <code class="highlighter-rouge">List</code> of the related instance type:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class _Question {
  @primaryKey
  int index;

  String description;

  @Relationship.hasMany("question")
  List answers;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that <code class="highlighter-rouge">answers</code> was pluralized and therefore the <code class="highlighter-rouge">belongsTo</code> side of the relationship would need to update its inverse key.

Join queries can be nested indefinitely, and can have their own matchers applied to them using the same syntax. For example, this would return a social media user with a specific ID, all of their posts in the since the beginning of 2016, and all of the people that have liked their post who have the name ‘Fred’:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">var query = new UserQuery()
    ..id = whereEqualTo(24)
    ..posts.single.postDate = whereGreaterThanEqualTo(new DateTime(2016).toUtc())
    ..posts.single.likers.single.name = whereContains("Fred");</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Which, when fetched and then encoded to JSON, would look something like this:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">{
    "id" : 24,
    "name" : "Somebody",
    "posts" : [{
        "id" : 4,
        "postDate" : "2016-01-02...",
        "likers" : [{
            "id" : 18,
            "name" : "Fred Freddieson"
        ]}
    }]
}</code></pre>
[/av_textblock]

[/av_three_fourth][/av_section][av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']
[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='What Comes Next?' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[/av_three_fourth][av_one_full first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_hr class='invisible' height='-30' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']

[av_codeblock wrapper_element='' wrapper_element_attributes='']

<!--HubSpot Call-to-Action Code -->
<span id="hs-cta-wrapper-1d38953e-bb7f-4eea-9001-0833028c58cc" class="hs-cta-wrapper">
<span id="hs-cta-1d38953e-bb7f-4eea-9001-0833028c58cc" class="hs-cta-node hs-cta-1d38953e-bb7f-4eea-9001-0833028c58cc">
<!-- [if lte IE 8]>
<div id="hs-cta-ie-element"></div>
<![endif]-->
<a href="http://cta-redirect.hubspot.com/cta/redirect/706489/1d38953e-bb7f-4eea-9001-0833028c58cc"><img id="hs-cta-img-1d38953e-bb7f-4eea-9001-0833028c58cc" class="hs-cta-img" style="border-width: 0px;" src="https://no-cache.hubspot.com/cta/default/706489/1d38953e-bb7f-4eea-9001-0833028c58cc.png" alt="CHAPTER 5: DEPLOYING AND OTHER FUN THINGS" /></a>
</span>
<script charset="utf-8" src="https://js.hscta.net/cta/current.js"></script>
<script type="text/javascript">
        hbspt.cta.load(706489, '1d38953e-bb7f-4eea-9001-0833028c58cc', {});
    </script>
</span>
<!-- end HubSpot Call-to-Action Code -->
[/av_codeblock]

[/av_one_full]
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

[/av_one_half][av_one_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth]
[/av_section]