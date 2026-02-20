---
aliases:
- "{%- if creators -%}{{creators[0].lastName or creators[0].name }}{%- if creators|length == 2 %} & {{creators[1].lastName or creators[1].name}}{% endif -%}{%- if creators|length > 2 %} et al.{% endif -%}{%- endif -%}{%- if date %} ({{date | format("YYYY")}}){% endif -%} {%- if shortTitle %} {{shortTitle | safe}} {%- else %} {{title | safe}} {%- endif -%}"
- "{{title | replace('"', "'")}}"
year: {{date | format("YYYY")}}
author: [{%- for c in creators -%}"[[People/{{c.firstName}} {{c.lastName}}|{{c.firstName}} {{c.lastName}}]]"{%- if not loop.last %}, {% endif -%}{%- endfor -%}]
citekey: {{citekey}}
{% if not tp.frontmatter.project %}project: {% endif %}
{% if not tp.frontmatter.hasTopic %}hasTopic: []{% endif %}
url: {{url}}
zoteroUri: {{desktopURI}}
---
# {{title}}

> [!abstract]-
{%- if abstractNote %}
> {{abstractNote|replace("\n","\n>")|striptags(true)}}
{%- endif %}

`BUTTON[update-litnote]`

{% persist "notes" -%}
{% if isFirstImport %}
## Key takeaways
- 
{% endif %}
{% endpersist -%}
{% if annotations.length > 0 %}

---
## Reading notes

{%- set colorValueMap = {
  "#ff6666": {"heading": "❗️ Important", "symbol": "!"},
  "#5fb236": {"heading": "🧐 Further research", "symbol": "$"}
}
%}

{%- set grouped_annotations = annotations | groupby("color") -%}
{%- for color, colorValue in colorValueMap -%}
{%- if color in grouped_annotations -%}
{%- set annotations_list = grouped_annotations[color] -%}
{%- for annotation in annotations_list -%}
{%- set citationLink = '[(p. ' ~ annotation.pageLabel ~ ')' ~ '](' ~ annotation.desktopURI ~ ')' if annotation.pageLabel else '[Link](' ~ annotation.desktopURI ~ ')' -%}
{%- if loop.first %}

### {{colorValue.heading}}
{%- endif -%}

{%- if annotation.imageRelativePath %}{{ "\n" if not loop.first and annotations_list[loop.index0 - 1].imageRelativePath else "" }}
> [!cite]+ Image {{citationLink}}
> ![[{{annotation.imageRelativePath}}]]
{%- elif (annotation.comment or "").indexOf("todo ") !== -1 %}
- [ ] **{{annotation.comment | replace("todo ", "")}}**: {{annotation.annotatedText}} {{citationLink}}
{%- elif annotation.comment %}
- **{{annotation.comment}}**: {{annotation.annotatedText}} {{citationLink}}
{%- else %}
- {{annotation.annotatedText | replace(r/\s+/g, " ")}} {{citationLink}}
{%- endif -%}
{% endfor -%}
{% endif -%}
{%- endfor -%}
{% endif %}

---
#source/paper