# Action Reference

The following document contains a reference of all actions.

<!--translate:ignore-->
<div>
	{% for group in site.translations.actions.items()|sort(attribute='1.order') %}
		{% include "_actions_group.html" %}
	{% endfor %}
</div>
<!--translate:ignore-->
