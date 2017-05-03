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
