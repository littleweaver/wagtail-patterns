# Wagtail Patterns

## Orderable list of foreign pages

A `MainPage` uses an `InlinePanel` to administate an ordered list of `app.Page`s.
`MainPageCategories` defines the relationship between `app.Page` and `MainPage`.

```python
class MainPage(Page):
    content_panels = Page.content_panels + [
        InlinePanel('categories', label='Category Pages'),
    ]


class MainPageCategories(Orderable):
    page = ParentalKey('app.MainPage', related_name='categories')
    category = models.ForeignKey(
        'wagtailcore.Page',
        blank=True,
        null=True,
        on_delete=models.SET_NULL,
        related_name='+',
    )

    panels = [
        PageChooserPanel('category', 'app.Page'),
    ]
```

```html
<ul>
    {% for relation in page.categories.all %}
        <li>
            <a href="{{ relation.category.url }}">
                {{ relation.category.title }}
            </a>
        </li>
    {% endfor %}
</ul>
```

## Orderable list of foreign pages from Settings

```python
from wagtail.contrib.settings.models import BaseSetting, register_setting
from modelcluster.models import ClusterableModel


@register_setting
class SomeSettings(BaseSetting, ClusterableModel):
    panels = [
        InlinePanel('widgets'),
    ]


class WidgetSomeSettings(Orderable):
    some_setting = ParentalKey('app_label.SomeSettings', related_name='widgets')
    widget = ParentalKey('app_label.Widget', related_name='some_setting')

    panels = [
        PageChooserPanel('widget', 'app_label.Widget'),
    ]
```

See [Orderable list of foreign pages](#orderable-list-of-foreign-pages) for rendering
and [Programatically create Site with Settings](#programatically-create-site-with-settings) for creating development data tied to a setting.


## Programatically create an image from file

```python
from django.core.files.images import ImageFile
from wagtail.wagtailimages.models import Image

image = Image.objects.create(
    title='Sample Image',
    file=ImageFile(open('path/to/image.jpg', 'rb'), name='image.jpg'),
)
page = Page(image=image)
```

## Programatically create Site with Settings

```python
site = Site.objects.create(
    site_name='dev',
    hostname='localhost',
    port='8000',
    root_page=some_home_page,
    is_default_site=True,
)
settings = SomeSettings.for_site(site)
```
