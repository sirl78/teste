---
ID: 241
post_title: home2
author: sloan
post_date: 2017-03-21 22:24:17
post_excerpt: ""
layout: page
permalink: >
  http://aqueduct-stablekernel-com.stablekernel.staging.wpengine.com/home2/
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
[av_section min_height='' min_height_px='500px' padding='huge' shadow='no-shadow' bottom_border='no-border-styling' bottom_border_diagonal_color='#333333' bottom_border_diagonal_direction='' bottom_border_style='' id='' color='main_color' custom_bg='' src='http://aqueduct-stablekernel-com.stablekernel.staging.wpengine.com/wp-content/uploads/sites/3/2017/03/mainBG.png' attachment='242' attachment_size='full' attach='fixed' position='center center' repeat='contain' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='' custom_class='']
[av_content_slider heading='FEATURES' columns='1' animation='slide' navigation='dots' autoplay='true' interval='5' font_color='' color='#414042' custom_class='']
[av_content_slide title='ORM query-building using Hamcrest matcher style' link='' linktarget='']
@httpGet getProgrammersAndTestersNamedBob() async {
var query = new UserQuery()
..name = whereContains("Bob")
..job = whereIn(["programmer", "tester"]);
var users = await query.fetch();
return new Response.ok(users);
}
[/av_content_slide]
[av_content_slide title='ORM interface that supports multiple databases' link='' linktarget='']
@httpGet getUsers() async {
var query = new Query();
var users = await query.fetch();
return new Response.ok(users);
}
[/av_content_slide]
[av_content_slide title='Project template generation' link='' linktarget='']
pub global activate -s git https://github.com/stablekernel/wildfire.git
pub global run wildfire:ignite MyProject
[/av_content_slide]
[/av_content_slider]
[/av_section]

[av_section min_height='' min_height_px='500px' padding='huge' shadow='no-border-styling' bottom_border='no-border-styling' bottom_border_diagonal_color='#333333' bottom_border_diagonal_direction='' bottom_border_style='' id='' color='main_color' custom_bg='#414042' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='' custom_class='']
[av_one_full first min_height='' vertical_alignment='av-align-top' space='' margin='0px' margin_sync='true' padding='0px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='#414042' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_heading tag='h3' padding='30' heading='GETTING STARTED' color='custom-color-heading' style='blockquote modern-quote modern-centered' custom_font='#ffffff' size='22' subheading_active='' subheading_size='20' custom_class='']

The tutorials take you on a step-by-step journey through the fundamentals of using Aqueduct to build web servers.
Check out the Tutorials, the Github repo, view the API documentation and our template generator, Wildfire.
[/av_heading]

[/av_one_full][av_textblock size='' font_color='custom' color='#d6dadc' custom_class='']
<p style="text-align: center;">The tutorials take you on a step-by-step journey through the fundamentals of using Aqueduct to build web servers. Check out the Tutorials, the Github repo, view the API documentation and our template generator, Wildfire.</p>
[/av_textblock]

[av_one_fourth first min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='TUTORIALS' link='manually,http://stablekernel.github.io/aqueduct/tut/getting-started/' link_target='_blank' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='light' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='GITHUB' link='manually,https://github.com/stablekernel/aqueduct' link_target='_blank' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='light' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='DOCUMENTATION' link='manually,https://www.dartdocs.org/documentation/aqueduct/2.0.1/index.html' link_target='_blank' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='light' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='WILDFIRE' link='manually,http://' link_target='' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='light' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_fourth][av_hr class='invisible' height='-50' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='huge' shadow='no-border-styling' bottom_border='no-border-styling' bottom_border_diagonal_color='#333333' bottom_border_diagonal_direction='' bottom_border_style='' id='' color='main_color' custom_bg='#d6dadc' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='' custom_class='']
[av_one_full first min_height='' vertical_alignment='av-align-top' space='' margin='0px' margin_sync='true' padding='0px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_heading tag='h3' padding='30' heading='ABOUT AQUEDUCT' color='custom-color-heading' style='blockquote modern-quote modern-centered' custom_font='#414042' size='22' subheading_active='' subheading_size='20' custom_class='']

The tutorials take you on a step-by-step journey through the fundamentals of using Aqueduct to build web servers.
Check out the Tutorials, the Github repo, view the API documentation and our template generator, Wildfire.
[/av_heading]

[/av_one_full][av_textblock size='' font_color='custom' color='#414042' custom_class='']
<p class="p1" style="text-align: center;"><span class="s1">Aqueduct is an open-source, server-side web framework written in Google’s Dart language. Aqueduct promises faster development, experimentation and testing – without sacrificing power. This allows for faster feedback cycles and greater stability over time.</span></p>
[/av_textblock]

[av_one_full first min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='TAKE A TOUR' link='manually,http://stablekernel.github.io/aqueduct/#tour' link_target='_blank' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='dark' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_full][av_hr class='invisible' height='-50' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' bottom_border_diagonal_color='#333333' bottom_border_diagonal_direction='' bottom_border_style='' id='' color='main_color' custom_bg='#414042' src='http://aqueduct-stablekernel-com.stablekernel.staging.wpengine.com/wp-content/uploads/sites/3/2017/03/darkBG.png' attachment='256' attachment_size='full' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='' custom_class='']
[av_one_full first min_height='' vertical_alignment='av-align-top' space='' custom_margin='aviaTBcustom_margin' margin='60px' margin_sync='true' padding='0px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[/av_one_full][av_one_fourth first min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_image src='http://aqueduct-stablekernel-com.stablekernel.staging.wpengine.com/wp-content/uploads/sites/3/2017/03/powered_by@2x.png' attachment='257' attachment_size='full' align='center' styling='' hover='' link='' target='' caption='' font_size='' appearance='' overlay_opacity='0.4' overlay_color='#000000' overlay_text_color='#ffffff' animation='no-animation' custom_class=''][/av_image]

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px' padding_sync='true' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[/av_one_fourth][av_one_fourth min_height='' vertical_alignment='av-align-top' space='no_margin' margin='0px' margin_sync='true' padding='10px,10px,10px,10px' border='' border_color='' radius='0px' radius_sync='true' background_color='' src='' attachment='' attachment_size='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='' custom_class='']

[av_button label='SIGN UP FOR NEWS' link='manually,http://info.stablekernel.com/aqueduct-stable-kernel-newsletter?hsCtaTracking=38cddba6-7fda-475c-9b91-421388cbf122%7Ca54e2993-c5b9-48b2-a134-8b354f1715e7' link_target='_blank' size='medium' position='center' icon_select='no' icon='ue800' font='entypo-fontello' color='light' custom_bg='#444444' custom_font='#ffffff' custom_class='']

[/av_one_fourth][av_hr class='invisible' height='-50' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]