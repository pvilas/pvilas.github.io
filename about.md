---
layout: page
title: About en Pere Vilás
permalink: /about/
---

![Pere Vilás](https://s.gravatar.com/avatar/18850e39781804e81fe98de693a22732?s=80)

En Pere viu a sa illeta daurada d'[Eivissa](https://ca.wikipedia.org/wiki/Illa_d%27Eivissa) amb sa seva familia. Fa feina en informàtica, telecomunicacions i empresa ([perfil de Linkedin](https://es.linkedin.com/in/pvilas)).

Per a contactar a mi, enviar-me correu a pvilas *at* gmail *dot* com. També podeu fer servir aquesta [fitxa de contacte](#fitxa-de-contacte).

Us deix sa lletra de *Roqueta, sa meua roca* per que pugeu cantar una mica ;-).

### Roqueta, sa meua roca

##### Lletra i música del mestre Joan Gamisans Arabí
_composada a la Ciutat d'Eivissa el mes d'octubre de 1930 
per a l'Orfeó Eivissenc._

1 
(canten tots)
Eivissa, illeta daurada.\
Ramell de polida màgia\
on canta l'alba argentada,\
on canta el llevant ses gales.\


2
Capseta tota enjoiada\
enmig d'una llum mai vista.\
Llumeta que del cel mana,\
llumeta dels ulls de nina.\


3
Perla d'escuma marina.\
Lliri blanc que surt del mar.\
Cambra d'amor perfumada\
d'aures que vénen i van.\

4 (es repeteix l'estrofa 3)

5 (canta solista)
Roqueta, sa meua roca.\
Castell de sa gentilesa.\
Roqueta, sa meua roca.\
Palau de vida serena.\


6
Besades de ses ondines\
hi arriben amb l'ona brava\
portant els pits que sospiren\
l'encís de veus d'enyorança.\

7 (canten tots)
Roqueta, sa meua roca.\
Castell de sa gentilesa.\
Roqueta, sa meua roca.\
Palau de vida serena.\


8
Besades de ses ondines\
hi arriben amb l'ona brava\
portant els pits que sospiren\
l'encís de veus d'enyorança.\


9
Perla d'escuma marina.\
Lliri blanc que surt del mar.\
Cambra d'amor perfumada\
d'aures que vénen i van.\

10 (es repeteix l'estrofa 9)

11
D'aures que venen i van.\
Lliri blanc que surt del mar.\
Cambra d'amor perfumada\
d'aures que vénen i van,\
d'aures que vénen i van.\

### Fitxa de contacte 


<div class="py2">
  {% if site.ajaxify_contact_form %}
    <form class="form-stacked">
      <input type="text" name="email" class="field-light" placeholder="Email Address">
      <textarea type="text" name="content" class="field-light" rows="5" placeholder="What would you like to say?"></textarea>
      <input type="hidden" name="_subject" value="New submission!" />
      <input type="text" name="_gotcha" style="display:none" />
      <button type='submit' class="button button-blue button-big mobile-block">Say Hello</button>
    </form>
  {% else %}
    <form action="https://formspree.io/{{ site.email }}" method="POST" class="form-stacked">
      <input type="text" name="email" class="field-light" placeholder="Email Address">
      <textarea type="text" name="content" class="field-light" rows="5" placeholder="What would you like to say?"></textarea>
      <input type="hidden" name="_next" value="{{ site.baseurl }}/thanks/" />
      <input type="hidden" name="_subject" value="New submission!" />
      <input type="text" name="_gotcha" style="display:none" />
      <input type="submit" class="button button-blue button-big mobile-block" value="Say Hello">
    </form>
  {% endif %}
</div>

{% if site.ajaxify_contact_form %}
  {% include ajaxify_content_form.html %}
{% endif %}


