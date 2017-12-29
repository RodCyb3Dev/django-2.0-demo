Third-party library support for older version of Django¶
Following the release of Django 2.0, we suggest that third-party app authors drop support for all versions of Django prior to 1.11. At that time, you should be able to run your package’s tests using python -Wd so that deprecation warnings do appear. After making the deprecation warning fixes, your app should be compatible with Django 2.0.

What’s new in Django 2.0
Simplified URL routing syntax
The new django.urls.path() function allows a simpler, more readable URL routing syntax. For example, this example from previous Django releases:

url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
could be written as:

path('articles/<int:year>/', views.year_archive),
The new syntax supports type coercion of URL parameters. In the example, the view will receive the year keyword argument as an integer rather than as a string. Also, the URLs that will match are slightly less constrained in the rewritten example. For example, the year 10000 will now match since the year integers aren’t constrained to be exactly four digits long as they are in the regular expression.

The django.conf.urls.url() function from previous versions is now available as django.urls.re_path(). The old location remains for backwards compatibility, without an imminent deprecation. The old django.conf.urls.include() function is now importable from django.urls so you can use from django.urls import include, path, re_path in your URLconfs.

The URL dispatcher document is rewritten to feature the new syntax and provide more details.

Mobile-friendly contrib.admin
The admin is now responsive and supports all major mobile devices. Older browsers may experience varying levels of graceful degradation.

Window expressions
The new Window expression allows adding an OVER clause to querysets. You can use window functions and aggregate functions in the expression.

Minor features
django.contrib.admin
The new ModelAdmin.autocomplete_fields attribute and ModelAdmin.get_autocomplete_fields() method allow using an Select2 search widget for ForeignKey and ManyToManyField.
django.contrib.auth
The default iteration count for the PBKDF2 password hasher is increased from 36,000 to 100,000.
django.contrib.gis
Added MySQL support for the AsGeoJSON function, GeoHash function, IsValid function, isvalid lookup, and distance lookups.
Added the Azimuth and LineLocatePoint functions, supported on PostGIS and SpatiaLite.
Any GEOSGeometry imported from GeoJSON now has its SRID set.
Added the OSMWidget.default_zoom attribute to customize the map’s default zoom level.
Made metadata readable and editable on rasters through the metadata, info, and metadata attributes.
Allowed passing driver-specific creation options to GDALRaster objects using papsz_options.
Allowed creating GDALRaster objects in GDAL’s internal virtual filesystem. Rasters can now be created from and converted to binary data in-memory.
The new GDALBand.color_interp() method returns the color interpretation for the band.
django.contrib.postgres
The new distinct argument for ArrayAgg determines if concatenated values will be distinct.
The new RandomUUID database function returns a version 4 UUID. It requires use of PostgreSQL’s pgcrypto extension which can be activated using the new CryptoExtension migration operation.
django.contrib.postgres.indexes.GinIndex now supports the fastupdate and gin_pending_list_limit parameters.
The new GistIndex class allows creating GiST indexes in the database. The new BtreeGistExtension migration operation installs the btree_gist extension to add support for operator classes that aren’t built-in.
inspectdb can now introspect JSONField and various RangeFields (django.contrib.postgres must be in INSTALLED_APPS).
django.contrib.sitemaps
Added the protocol keyword argument to the GenericSitemap constructor.
Cache
cache.set_many() now returns a list of keys that failed to be inserted. For the built-in backends, failed inserts can only happen on memcached.
File Storage
File.open() can be used as a context manager, e.g. with file.open() as f:.
Forms
The new date_attrs and time_attrs arguments for SplitDateTimeWidget and SplitHiddenDateTimeWidget allow specifying different HTML attributes for the DateInput and TimeInput (or hidden) subwidgets.
The new Form.errors.get_json_data() method returns form errors as a dictionary suitable for including in a JSON response.
Generic Views
The new ContextMixin.extra_context attribute allows adding context in View.as_view().
Management Commands
inspectdb now translates MySQL’s unsigned integer columns to PositiveIntegerField or PositiveSmallIntegerField.
The new makemessages --add-location option controls the comment format in PO files.
loaddata can now read from stdin.
The new diffsettings --output option allows formatting the output in a unified diff format.
On Oracle, inspectdb can now introspect AutoField if the column is created as an identity column.
On MySQL, dbshell now supports client-side TLS certificates.
Migrations
The new squashmigrations --squashed-name option allows naming the squashed migration.
Models
The new StrIndex database function finds the starting index of a string inside another string.
On Oracle, AutoField and BigAutoField are now created as identity columns.
The new chunk_size parameter of QuerySet.iterator() controls the number of rows fetched by the Python database client when streaming results from the database. For databases that don’t support server-side cursors, it controls the number of results Django fetches from the database adapter.
QuerySet.earliest(), QuerySet.latest(), and Meta.get_latest_by now allow ordering by several fields.
Added the ExtractQuarter function to extract the quarter from DateField and DateTimeField, and exposed it through the quarter lookup.
Added the TruncQuarter function to truncate DateField and DateTimeField to the first day of a quarter.
Added the db_tablespace parameter to class-based indexes.
If the database supports a native duration field (Oracle and PostgreSQL), Extract now works with DurationField.
Added the of argument to QuerySet.select_for_update(), supported on PostgreSQL and Oracle, to lock only rows from specific tables rather than all selected tables. It may be helpful particularly when select_for_update() is used in conjunction with select_related().
The new field_name parameter of QuerySet.in_bulk() allows fetching results based on any unique model field.
CursorWrapper.callproc() now takes an optional dictionary of keyword parameters, if the backend supports this feature. Of Django’s built-in backends, only Oracle supports it.
The new connection.execute_wrapper() method allows installing wrappers around execution of database queries.
The new filter argument for built-in aggregates allows adding different conditionals to multiple aggregations over the same fields or relations.
Added support for expressions in Meta.ordering.
The new named parameter of QuerySet.values_list() allows fetching results as named tuples.
The new FilteredRelation class allows adding an ON clause to querysets.
Pagination
Added Paginator.get_page() to provide the documented pattern of handling invalid page numbers.
Requests and Responses
The runserver Web server supports HTTP 1.1.
Templates
To increase the usefulness of Engine.get_default() in third-party apps, it now returns the first engine if multiple DjangoTemplates engines are configured in TEMPLATES rather than raising ImproperlyConfigured.
Custom template tags may now accept keyword-only arguments.
Tests
Added threading support to LiveServerTestCase.
Added settings that allow customizing the test tablespace parameters for Oracle: DATAFILE_SIZE, DATAFILE_TMP_SIZE, DATAFILE_EXTSIZE, and DATAFILE_TMP_EXTSIZE.
Validators
The new ProhibitNullCharactersValidator disallows the null character in the input of the CharField form field and its subclasses. Null character input was observed from vulnerability scanning tools. Most databases silently discard null characters, but psycopg2 2.7+ raises an exception when trying to save a null character to a char/text field with PostgreSQL.
Backwards incompatible changes in 2.0
Removed support for bytestrings in some places
To support native Python 2 strings, older Django versions had to accept both bytestrings and unicode strings. Now that Python 2 support is dropped, bytestrings should only be encountered around input/output boundaries (handling of binary fields or HTTP streams, for example). You might have to update your code to limit bytestring usage to a minimum, as Django no longer accepts bytestrings in certain code paths.

For example, reverse() now uses str() instead of force_text() to coerce the args and kwargs it receives, prior to their placement in the URL. For bytestrings, this creates a string with an undesired b prefix as well as additional quotes (str(b'foo') is "b'foo'"). To adapt, call decode() on the bytestring before passing it to reverse().

Database backend API
This section describes changes that may be needed in third-party database backends.

The DatabaseOperations.datetime_cast_date_sql(), datetime_cast_time_sql(), datetime_trunc_sql(), datetime_extract_sql(), and date_interval_sql() methods now return only the SQL to perform the operation instead of SQL and a list of parameters.
Third-party database backends should add a DatabaseWrapper.display_name attribute with the name of the database that your backend works with. Django may use it in various messages, such as in system checks.
The first argument of SchemaEditor._alter_column_type_sql() is now model rather than table.
The first argument of SchemaEditor._create_index_name() is now table_name rather than model.
To enable FOR UPDATE OF support, set DatabaseFeatures.has_select_for_update_of = True. If the database requires that the arguments to OF be columns rather than tables, set DatabaseFeatures.select_for_update_of_column = True.
To enable support for Window expressions, set DatabaseFeatures.supports_over_clause to True. You may need to customize the DatabaseOperations.window_start_rows_start_end() and/or window_start_range_start_end() methods.
Third-party database backends should add a DatabaseOperations.cast_char_field_without_max_length attribute with the database data type that will be used in the Cast function for a CharField if the max_length argument isn’t provided.
The first argument of DatabaseCreation._clone_test_db() and get_test_db_clone_settings() is now suffix rather than number (in case you want to rename the signatures in your backend for consistency). django.test also now passes those values as strings rather than as integers.
Third-party database backends should add a DatabaseIntrospection.get_sequences() method based on the stub in BaseDatabaseIntrospection.
Dropped support for Oracle 11.2
The end of upstream support for Oracle 11.2 is Dec. 2020. Django 1.11 will be supported until April 2020 which almost reaches this date. Django 2.0 officially supports Oracle 12.1+.

Default MySQL isolation level is read committed
MySQL’s default isolation level, repeatable read, may cause data loss in typical Django usage. To prevent that and for consistency with other databases, the default isolation level is now read committed. You can use the DATABASES setting to use a different isolation level, if needed.

AbstractUser.last_name max_length increased to 150
A migration for django.contrib.auth.models.User.last_name is included. If you have a custom user model inheriting from AbstractUser, you’ll need to generate and apply a database migration for your user model.

If you want to preserve the 30 character limit for last names, use a custom form:

from django.contrib.auth.forms import UserChangeForm

class MyUserChangeForm(UserChangeForm):
    last_name = forms.CharField(max_length=30, required=False)
If you wish to keep this restriction in the admin when editing users, set UserAdmin.form to use this form:

from django.contrib.auth.admin import UserAdmin
from django.contrib.auth.models import User

class MyUserAdmin(UserAdmin):
    form = MyUserChangeForm

admin.site.unregister(User)
admin.site.register(User, MyUserAdmin)
QuerySet.reverse() and last() are prohibited after slicing
Calling QuerySet.reverse() or last() on a sliced queryset leads to unexpected results due to the slice being applied after reordering. This is now prohibited, e.g.:

>>> Model.objects.all()[:2].reverse()
Traceback (most recent call last):
...
TypeError: Cannot reverse a query once a slice has been taken.
Form fields no longer accept optional arguments as positional arguments
To help prevent runtime errors due to incorrect ordering of form field arguments, optional arguments of built-in form fields are no longer accepted as positional arguments. For example:

forms.IntegerField(25, 10)
raises an exception and should be replaced with:

forms.IntegerField(max_value=25, min_value=10)
call_command() validates the options it receives
call_command() now validates that the argument parser of the command being called defines all of the options passed to call_command().

For custom management commands that use options not created using parser.add_argument(), add a stealth_options attribute on the command:

class MyCommand(BaseCommand):
    stealth_options = ('option_name', ...)
Indexes no longer accept positional arguments
For example:

models.Index(['headline', '-pub_date'], 'index_name')
raises an exception and should be replaced with:

models.Index(fields=['headline', '-pub_date'], name='index_name')
Foreign key constraints are now enabled on SQLite
This will appear as a backwards-incompatible change (IntegrityError: FOREIGN KEY constraint failed) if attempting to save an existing model instance that’s violating a foreign key constraint.

Foreign keys are now created with DEFERRABLE INITIALLY DEFERRED instead of DEFERRABLE IMMEDIATE. Thus, tables may need to be rebuilt to recreate foreign keys with the new definition, particularly if you’re using a pattern like this:

from django.db import transaction

with transaction.atomic():
    Book.objects.create(author_id=1)
    Author.objects.create(id=1)
If you don’t recreate the foreign key as DEFERRED, the first create() would fail now that foreign key constraints are enforced.

Backup your database first! After upgrading to Django 2.0, you can then rebuild tables using a script similar to this:

from django.apps import apps
from django.db import connection

for app in apps.get_app_configs():
    for model in app.get_models(include_auto_created=True):
        if model._meta.managed and not (model._meta.proxy or model._meta.swapped):
            for base in model.__bases__:
                if hasattr(base, '_meta'):
                    base._meta.local_many_to_many = []
            model._meta.local_many_to_many = []
            with connection.schema_editor() as editor:
                editor._remake_table(model)
This script hasn’t received extensive testing and needs adaption for various cases such as multiple databases. Feel free to contribute improvements.

In addition, because of a table alteration limitation of SQLite, it’s prohibited to perform RenameModel and RenameField operations on models or fields referenced by other models in a transaction. In order to allow migrations containing these operations to be applied, you must set the Migration.atomic attribute to False.

Miscellaneous
The SessionAuthenticationMiddleware class is removed. It provided no functionality since session authentication is unconditionally enabled in Django 1.10.

The default HTTP error handlers (handler404, etc.) are now callables instead of dotted Python path strings. Django favors callable references since they provide better performance and debugging experience.

RedirectView no longer silences NoReverseMatch if the pattern_name doesn’t exist.

When USE_L10N is off, FloatField and DecimalField now respect DECIMAL_SEPARATOR and THOUSAND_SEPARATOR during validation. For example, with the settings:

USE_L10N = False
USE_THOUSAND_SEPARATOR = True
DECIMAL_SEPARATOR = ','
THOUSAND_SEPARATOR = '.'
an input of "1.345" is now converted to 1345 instead of 1.345.

Subclasses of AbstractBaseUser are no longer required to implement get_short_name() and get_full_name(). (The base implementations that raise NotImplementedError are removed.) django.contrib.admin uses these methods if implemented but doesn’t require them. Third-party apps that use these methods may want to adopt a similar approach.

The FIRST_DAY_OF_WEEK and NUMBER_GROUPING format settings are now kept as integers in JavaScript and JSON i18n view outputs.

assertNumQueries() now ignores connection configuration queries. Previously, if a test opened a new database connection, those queries could be included as part of the assertNumQueries() count.

The default size of the Oracle test tablespace is increased from 20M to 50M and the default autoextend size is increased from 10M to 25M.

To improve performance when streaming large result sets from the database, QuerySet.iterator() now fetches 2000 rows at a time instead of 100. The old behavior can be restored using the chunk_size parameter. For example:

Book.objects.iterator(chunk_size=100)
Providing unknown package names in the packages argument of the JavaScriptCatalog view now raises ValueError instead of passing silently.

A model instance’s primary key now appears in the default Model.__str__() method, e.g. Question object (1).

makemigrations now detects changes to the model field limit_choices_to option. Add this to your existing migrations or accept an auto-generated migration for fields that use it.

Performing queries that require automatic spatial transformations now raises NotImplementedError on MySQL instead of silently using non-transformed geometries.

django.core.exceptions.DjangoRuntimeWarning is removed. It was only used in the cache backend as an intermediate class in CacheKeyWarning’s inheritance of RuntimeWarning.

Renamed BaseExpression._output_field to output_field. You may need to update custom expressions.

In older versions, forms and formsets combine their Media with widget Media by concatenating the two. The combining now tries to preserve the relative order of elements in each list. MediaOrderConflictWarning is issued if the order can’t be preserved.

django.contrib.gis.gdal.OGRException is removed. It’s been an alias for GDALException since Django 1.8.

Support for GEOS 3.3.x is dropped.

The way data is selected for GeometryField is changed to improve performance, and in raw SQL queries, those fields must now be wrapped in connection.ops.select. See the Raw queries note in the GIS tutorial for an example.

Features deprecated in 2.0
context argument of Field.from_db_value() and Expression.convert_value()
The context argument of Field.from_db_value() and Expression.convert_value() is unused as it’s always an empty dictionary. The signature of both methods is now:

(self, value, expression, connection)
instead of:

(self, value, expression, connection, context)
Support for the old signature in custom fields and expressions remains until Django 3.0.

Miscellaneous
The django.db.backends.postgresql_psycopg2 module is deprecated in favor of django.db.backends.postgresql. It’s been an alias since Django 1.9. This only affects code that imports from the module directly. The DATABASES setting can still use 'django.db.backends.postgresql_psycopg2', though you can simplify that by using the 'django.db.backends.postgresql' name added in Django 1.9.
django.shortcuts.render_to_response() is deprecated in favor of django.shortcuts.render(). render() takes the same arguments except that it also requires a request.
The DEFAULT_CONTENT_TYPE setting is deprecated. It doesn’t interact well well with third-party apps and is obsolete since HTML5 has mostly superseded XHTML.
HttpRequest.xreadlines() is deprecated in favor of iterating over the request.
The field_name keyword argument to QuerySet.earliest() and QuerySet.latest() is deprecated in favor of passing the field names as arguments. Write .earliest('pub_date') instead of .earliest(field_name='pub_date').
Features removed in 2.0
These features have reached the end of their deprecation cycle and are removed in Django 2.0.

See Features deprecated in 1.9 for details on these changes, including how to remove usage of these features.

The weak argument to django.dispatch.signals.Signal.disconnect() is removed.
django.db.backends.base.BaseDatabaseOperations.check_aggregate_support() is removed.
The django.forms.extras package is removed.
The assignment_tag helper is removed.
The host argument to SimpleTestCase.assertsRedirects() is removed. The compatibility layer which allows absolute URLs to be considered equal to relative ones when the path is identical is also removed.
Field.rel and Field.remote_field.to are removed.
The on_delete argument for ForeignKey and OneToOneField is now required in models and migrations. Consider squashing migrations so that you have fewer of them to update.
django.db.models.fields.add_lazy_relation() is removed.
When time zone support is enabled, database backends that don’t support time zones no longer convert aware datetimes to naive values in UTC anymore when such values are passed as parameters to SQL queries executed outside of the ORM, e.g. with cursor.execute().
django.contrib.auth.tests.utils.skipIfCustomUser() is removed.
The GeoManager and GeoQuerySet classes are removed.
The django.contrib.gis.geoip module is removed.
The supports_recursion check for template loaders is removed from:
django.template.engine.Engine.find_template()
django.template.loader_tags.ExtendsNode.find_template()
django.template.loaders.base.Loader.supports_recursion()
django.template.loaders.cached.Loader.supports_recursion()
The load_template and load_template_sources template loader methods are removed.
The template_dirs argument for template loaders is removed:
django.template.loaders.base.Loader.get_template()
django.template.loaders.cached.Loader.cache_key()
django.template.loaders.cached.Loader.get_template()
django.template.loaders.cached.Loader.get_template_sources()
django.template.loaders.filesystem.Loader.get_template_sources()
django.template.loaders.base.Loader.__call__() is removed.
Support for custom error views that don’t accept an exception parameter is removed.
The mime_type attribute of django.utils.feedgenerator.Atom1Feed and django.utils.feedgenerator.RssFeed is removed.
The app_name argument to include() is removed.
Support for passing a 3-tuple (including admin.site.urls) as the first argument to include() is removed.
Support for setting a URL instance namespace without an application namespace is removed.
Field._get_val_from_obj() is removed.
django.template.loaders.eggs.Loader is removed.
The current_app parameter to the contrib.auth function-based views is removed.
The callable_obj keyword argument to SimpleTestCase.assertRaisesMessage() is removed.
Support for the allow_tags attribute on ModelAdmin methods is removed.
The enclosure keyword argument to SyndicationFeed.add_item() is removed.
The django.template.loader.LoaderOrigin and django.template.base.StringOrigin aliases for django.template.base.Origin are removed.
See Features deprecated in 1.10 for details on these changes.

The makemigrations --exit option is removed.
Support for direct assignment to a reverse foreign key or many-to-many relation is removed.
The get_srid() and set_srid() methods of django.contrib.gis.geos.GEOSGeometry are removed.
The get_x(), set_x(), get_y(), set_y(), get_z(), and set_z() methods of django.contrib.gis.geos.Point are removed.
The get_coords() and set_coords() methods of django.contrib.gis.geos.Point are removed.
The cascaded_union property of django.contrib.gis.geos.MultiPolygon is removed.
django.utils.functional.allow_lazy() is removed.
The shell --plain option is removed.
The django.core.urlresolvers module is removed in favor of its new location, django.urls.
CommaSeparatedIntegerField is removed, except for support in historical migrations.
The template Context.has_key() method is removed.
Support for the django.core.files.storage.Storage.accessed_time(), created_time(), and modified_time() methods is removed.
Support for query lookups using the model name when Meta.default_related_name is set is removed.
The MySQL __search lookup is removed.
The shim for supporting custom related manager classes without a _apply_rel_filters() method is removed.
Using User.is_authenticated() and User.is_anonymous() as methods rather than properties is no longer supported.
The Model._meta.virtual_fields attribute is removed.
The keyword arguments virtual_only in Field.contribute_to_class() and virtual in Model._meta.add_field() are removed.
The javascript_catalog() and json_catalog() views are removed.
django.contrib.gis.utils.precision_wkt() is removed.
In multi-table inheritance, implicit promotion of a OneToOneField to a parent_link is removed.
Support for Widget._format_value() is removed.
FileField methods get_directory_name() and get_filename() are removed.
The mark_for_escaping() function and the classes it uses: EscapeData, EscapeBytes, EscapeText, EscapeString, and EscapeUnicode are removed.
The escape filter now uses django.utils.html.conditional_escape().
Manager.use_for_related_fields is removed.
Model Manager inheritance follows MRO inheritance rules. The requirement to use Meta.manager_inheritance_from_future to opt-in to the behavior is removed.
Support for old-style middleware using settings.MIDDLEWARE_CLASSES is removed.
