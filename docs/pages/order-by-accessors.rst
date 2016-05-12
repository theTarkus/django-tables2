.. _order-by-accessors:

Alternative column ordering
===========================

When using queryset data, one might want to show a computed value which is not
in the database. In this case, attempting to order the column will cause an
exception::

    # models.py
    class Person(models.Model):
        first_name = models.CharField(max_length=200)
        family_name = models.CharField(max_length=200)

        @property
        def name(self):
            return '{} {}'.format(self.first_name, self.family_name)

    # tables.py
    class PersonTable(tables.Table):
        name = tables.Column()

::

    >>> table = PersonTable(Person.objects.all())
    >>> table.order_by = 'name'
    >>>
    >>> # will result in:
    FieldError: Cannot resolve keyword 'name' into field. Choices are: first_name, family_name

To prevent this, django-tables2 needs a hint to know how to sort that column:
you need to supply an ``order_by`` argument containing a name or a tuple of the
names of the columns the database should use to sort it::

    class PersonTable(tables.Table):
        name = tables.Column(order_by=('first_name', 'family_name'))

`~.Accessor` syntax can be used as well, as long as they point to a model field.

If ordering does not make sense for a particular column, it can be disabled via
the ``orderable`` argument::

    class SimpleTable(tables.Table):
        name = tables.Column()
        actions = tables.Column(orderable=False)
