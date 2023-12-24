{% assign oneday = 86400 %}
{% assign week-class-day = 0 %}
{% assign days-to-next-class = "2, 5" | split: ", " %}
{% assign next-class-date = "August 22, 2023, 12pm" | date: "%s" %}

<table class="condensed">
    <thead>
        <tr>
            <th class="text-center">Week</th>
            <th class="text-center">Topic/Reading</th>
            <th class="text-center">Assignment</th>
        </tr>
    </thead>
    <tbody>
        {% for entry in site.data.schedule %}
            {% assign today-date = next-class-date %}
            {% unless entry.date or entry.type == "module" %}
                {% assign next-class-date = days-to-next-class[week-class-day] | times: oneday | plus: today-date %}
                {% assign week-class-day = week-class-day | plus: 1 | modulo: days-to-next-class.size %}
            {% endunless %}
            {% if entry.type != "skip" %}
                <tr>
                    {% if entry.type == "module" %}
                        <td class="text-center highlight" colspan="3">{{ entry.topic }}</td>
                    {% else %}
                        <td class="text-center">
                            {% if entry.date %}
                                {{ entry.date }}
                            {% else %}
                                {{ today-date | date: '%b%d' }}
                            {% endif %}
                        </td>
                        <td>
                            {% if entry.type == "no-class" %}
                                No Class ({{ entry.topic }})
                            {% elsif entry.type == "no-lab" %}
                                No Lab ({{ entry.topic }})
                            {% elsif entry.type == "exam" %}
                                <mark>{{ entry.topic }}</mark>
                            {% else %}
                                {{ entry.topic }}
                                {% if entry.req-readings or entry.opt-readings%}
                                    <ul>
                                        {% if entry.req-readings %}
                                            {{ entry.req-readings | 
                                                append: site.data.refs.named-links | 
                                                markdownify | 
                                                replace: '<ul>', '' | 
                                                replace: '</ul>', '' }}
                                        {% endif %}
                                        {% if entry.opt-readings %}
                                            {{ entry.opt-readings | 
                                                append: site.data.refs.named-links | 
                                                markdownify | 
                                                replace: '<ul>', '' | 
                                                replace: '</ul>', '' | 
                                                replace: '<li>', '<li class="optional">' }}
                                        {% endif %}
                                    </ul>
                                {% endif %}
                            {% endif %}
                        </td>
                        <td>
                            {{ entry.assignment }}
                            {% if entry.assignment-due %}
                                {% assign due-date = oneday | times: entry.assignment-due | plus: today-date %}
                                due {{ due-date | date: '%b%d' }}
                            {% endif %}
                            {% if entry.assignment-due-next %}
                                {% if today == "Tue" %}
                                    {% assign due-date = oneday | times: 6 | plus: today-date %}
                                {% else %}                            
                                    {% assign due-date = oneday | times: 8 | plus: today-date %}
                                {% endif %}
                                due {{ due-date | date: '%b%d' }}
                            {% endif %}
                        </td>
                    {% endif %}
                </tr>
            {% endif %}
        {% endfor %}
    </tbody>
</table>

{{ site.data.references.links }}
