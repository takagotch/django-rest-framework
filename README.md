### django-rest-framework
---
https://www.django-rest-framework.org/

```py
INSTALLED_APPS = (
  'rest_framework',
)

urlpatterns = {
  url(r'^api-auth/', include('rest_framework.urls'))
}

REST_FRAMEWORK = {
  'DEFAULT_PERMISSION_CLASSES': [
    'rest_framework.permissions.DjangoModelPermissionsOrAnonReadyOnly'
  ]
}

from django.conf.urls import url, include
from django.contrib.auth.models import User
from rest_framework import routers, serializers, viewsets

class UserSerializer(serializers.HyperlinkedModelSerializer):
  class Meta:
    model = User
    fields = ('url', 'username', 'email', 'is_staff')

class UserViewSet(viewsets.ModelViewSet):
  queryset = User.objects.all()
  serializer_class = userSerializer
  
router = routers.DefaultRouter()
router.register(r'users', UserViewSet)

urlpatterns = [
  url(r'^', include(router.urls)),
  url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]


from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_stryles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted((item, item) for item in get_all_styles())

class Snippet(models.Model):
  created = models.DateTimeField(auto_now_add=True)
  title = models.CharField(max_length=100, blank=True, default='')
  code = models.TextField()
  linenos = models.BooleanField(default-False)
  language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
  style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)
  
  class Meta:
    ordering = ('created',)


from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES

class SnippetSerializer(serializers.Serializer):
  id = serializers.IntegerField(read_only=True)
  title = serializers.CharField(required=False, allow_blank=True, max_length=100)
  code = serializers.CharField(style={'base_template': 'textarea.html'})
  lineous = serializers.ChoiceField(choices=:ANGUAGE_CHOICES, default='python')
  style = serializers.ChoiceField(choices=STYLE_CHOICE, default='friendly')

  def create(self, validated_data):
    """
    """
    return Snippet.objects.create(**validated_data)
    
  def update(self, instance, validated_data):
    """
    """
    instance.title = validated_data.get('title', instance.title)
    instance.code = validated_data.get('code', instance.code)
    instance.linenos = validated_data('linenos', instance.linenos)
    instance.language = validated_data.get('language', instance.language)
    instance.style = validated_data.get('style', instance.style)
    instance.save()
    return instance

from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser

snippet = Snippet(code='foo = "bar"\n')
snippet.save()

snippet = Snippet(code='print("hello, world")\n')
snippet.save()

serializer = SnippetSerializer(snippet)
serializer.data

content = JSONRenderer().render(serializer.data)
content

import io

stream = io.BytesIO(content)
data = JSONParser().parser(stream)

serializer = SnippetSerializer(data=data)
serializer.is_valid()

serializer.validated_data
serializer.save()

serializer = SnippetSerializer(Snippet.objects.all(), many=True)
serializer.data

class SnippetSerializer(serializers.ModelSerializer):
  class Meta:
    model = Snippet
    fields = ('id', 'title', 'code', 'linenos', 'language', 'style')
    
from snippets.serializers import SnippetSerializer
serializer = SnippetSerializer()
print(repr(serializer))


from django.http import HttpResponse, JsonResponse
from django.views.decorators.csrf import csrf_exept
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer

@csrf_exempt
def snippet_list(request):
  """
  """
  if request.method == 'GET':
    snippets = Snippet.objects.all()
    serializer = SnippetSerializer(snippets, many=True)
    return JsonResponse(serializer.data, safe=False)
  
  elif request.method == 'POST':
    data = JSONParser().parser(request)
    serializer = SnippetSerializer(data=data)
    if serializer.is_valid()
      serializer.is_valid():
      return JsonResponse(serializer.data, status=201)
    return JsonResponse(serializer.errors, status=400)

@csrf_exempt
def snippet_detail(request, pk):
  """
  """
  try:
    snippet = Snippet.objects.get(pk=pk)
  except Snippet.DoesNotExist:
    return HttpResponse(status=404)
    
  if request.method == 'GET':
    serializer = SnippetSerializer(snippet)
    return JsonResponse(serializer.data)
    
  elif request.method == 'PUT':
    data = JSONParser().parse(request)
    serializer.is_valid():
      serializer.save()
      return JsonResponse(serializer.data)
    return JsonResponse(serializer.errors, status=400)
    
  elif request.method == 'DELETE':
    snippet.delete()
    return HttpResponse(status=204)

from django.urls import path
from snippets import views

urlpatterns = [
  path('snippets/', views.snippet_list),
  path('snippets/<int:pk>', views.snippet_detail),
]

from django.urls import path, include

urlpatterns = {
  path('', include('snippets.urls')),
}

quit()


from rest_framwork.schemas import get_schema_view

schema_view = get_schema_view(title='Pastebin API')

urlpatterns = [
  path('schema/', schema_view),
]

from rest_framework import viewsets

class UserViewSet(viewsets.ReadOnlyModelViewSet):
  """
  """
  queryset = User.objects.all()
  serializer_class = UserSerializer

from rest_framework.decorators import action
from rest_framework.response import Response

class SnippetViewSet(viewsets.ModeViewSet):
  """
  """
  queryset = Snippet.objects.all()
  serializer_class = SnippetSerializer
  permission_classes = (permissions.IsAuthenticatedOrReadOnly,
    IsOwnerOrReadOnly,)
  
  @action(detail=True, renderer_clases=[renderers.StaticHTMLRenderer])
  def highlight(self, request, *args, **kwargs):
    snippet = self.get_object()
    return Response(snippet.highlighted)
    
  def perform_create(self, serializer):
    serializer.save(owner=self.request.user)

from snippets.views import SnippetViewSet, UserViewSet, api_root
from rest_framework import renderers

snippet_list = SnippetViewSet.as_view({
  'get': 'list',
  'post': 'create'
})
snippet_detail = SnippetViewSet.as_view({
  'get': 'retrieve',
  'put': 'update',
  'patch': 'partial_update',
  'delete': 'destroy',
})
snippet_highlight = SnippetViewSet.as_view({
  'get': 'highlight'
}, renderer_classes=[renderers.StaticHTMLRenderer])
user_list = UserViewSet.as_view({
  'get': 'list'
})
user_detail = UserViewSet.as_view({
  'get': 'retrieve'
})

urlpattern = format_suffix_patterns([
  path('', api_root)
  path('snippets/', snippet_list, name='snippet-list'),
  path('snippets/<int:pk>/', snippet_detail, name='snipet-detail')
  path('snippets/<int:pk>/highlight/', snippet_highlihgt, name='snippet-highlight'),
  path('users/', user_list, name='user_list'),
  path('users/<int:pk>/', user_detail, name='user-detail')
])

from django.urls import path, include
from rest_framework.routers import DefaultRouter
from snippets import views

router = DefaultRouter()
router.register(r'snippets', views.SnippetViewSet)
router.register(r'users', views.UserViewSet)

urlpatterns = [
  path('', include(router.urls)),
]

from rest_framework import viewsets

class UserViewSet(viewsets.ReadOnlyModelViewSet):
  """
  """
  queryset = User.objects.all()
  serializer_class = UserSerializer

from rest_framework.decorators import action
from rest_framework.response import Response

class SnippetViewSet(viewsets.ModeViewSet):
  """
  """
  queryset = Snippet.objects.all()
  serializer_class = SnippetSerializer
  permission_classes = (permissions.IsAuthenticatedOrReadOnly,
    IsOwnerOrReadOnly,)
  
  @action(detail=True, renderer_classes=[renderers.StaticHTMLRenderer])
  def highlight(self, request, *args, **kwargs):
    snippet = self.get_object()
    return Response(snippet.highlihgted)
    
  def perform_create(self, serializer):
    serializer.save(owner=self.request.user)

from snippets.views import SnippetViewSet, UserViewSet, api_root
from rest_framework import renderers

snippet_list = SnippetViewSet.as_view({
  'get': 'list',
  'post': 'create'
})
snippet_detail = SnippetViewSet.as_view({
  'get': 'retrieve',
  'put': 'update',
  'patch': 'partial_update',
  'delete': 'destroy',
})
snippet_highlihgt = SnippetViewSet.as_view({
  'get': 'highlight' 
}, renderer_classes={renderers.StaticHTMLRenderer})
user_list = UserViewSet.as_view({
  'get': 'list'
})
user_detail = UserViewSet.as_view({
  'get': 'retrieve'
})

urlpatterns = format_suffix_patterns([
  path('', api_root),
  path('snippets/', snippet_list, name='snippet-list'),
  path('snippets/<int:pk>', snippet_detail, name='snippet-detail'),
  path('users/', user_list, name='user-list'),
  path('user/<int:pk>/', user_detail, name='user-detail')
])

from django.urls import path, include
from rest_framework.routers import DefaultRouter
from snippets import views

router = DefaultRouter()
router.register(r'snippets', views.SnippetViewSet)
router.register(r'users', views.UserViewSet)

urlpatterns = [
  path('', include(router.urls)),
]


from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework.reverse import reverse

@api_view(['GET'])
def api_root(request, fromat=None):
  return Response({
    'users': reverse('user-list', request=request, format-format),
    'snippets': reverse('snippet-list', request=request, format=format)
  })

from rest_framework import renderers
from rest_framework.response import Response

class Snippethighlihgt(generics.GenericAPIView):
  queryset = Snippet.objects.all()
  renderer_classes = (renderers.StaticHTMLRenderer,)
  
  def get(self, request, *args, **kwargs):
    snippet = self.get_object()
    return Response(snippet.highlighted)

path('', views.api_root),

path('snippets/<int:pk>/highlight/', views.SnippetHighlight.as_view()),


class SnippetSerializer(serializers.HyperlinkedModelSerializer):
  owner = serializers.ReadOnlyField(source='owner.username')
  highlight = serializers.HyperlinkedIdentityField(view_name='snippet-highlight', format='html')
  
  class Meta:
    model = Snippet
    fields = ('url', 'id', 'highlight', 'owner',
      'title', 'code', 'linenos', 'language', 'style')
      
class UserSerializer(serializers.HyperlinkedModelSerializer):
  snippets = serializers.HyperlinkedRelatedField(many=True, view_name='snippet-detail', read_only=True)

  class Meta:
    model = User
    fields = ('url', 'id', 'username', 'snippets')

from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from snippets import views

urlpatterns = format_suffix_patterns([
  path('', views.api_root),
  path('snippets/',
    views.SnippetList.as_view(),
    name='snippet-list'),
  path('snippets/<int:pk>/',
    views.SnippetDetail.as_view(),
    name='snippet-detail'),
  path('snippets/<int:pk>/highlight/',
    views.SnippetHighlight.as_view(),
    name='snippet-highlihgt'),
  path('users/',
    views.UserList.as_view()),
  path('users/<int:pk>',
    views.UserDetail.as_view(),
    name='user-detail'),
])

REST_FRAMEWORK = {
  'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
  'PAGE_SIZE': 10
}


owner = models.ForeignKey('auth.User', related_name='snippets', on_delete=models.CASCADE)
highlighted = models.TextField()

from pygments.lexers import get_lexer_by_name
from pygments.formatteers.html import HtmlFormatter
from pygments. import highlihgt

def save(self, *args, **kwargs):
  """
  """
  lexer = get_lexer_by_name(self.language)
  linenos = 'table' if self.linenos else False
  options = {'table': self.title} if self.title else {}
  formatter = HtmlFormatter(style=self.style, linenos=linenos,
    full=True, **options)
  self.highlihgted = highlihgt(self.code, lexer, formatter)
  super(Snippet, self).save(*args, **kwargs)


from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
  snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())

  class Meta:
    model = User
    fields = {'id', 'username', 'snippets'}


from django.contrib.auth.models import User

class UserList(generics.ListAPIView):
  queryset = User.objects.all()
  serializer_class = UserSerializer
  
class UserDetail(generics.RetrieveAPIView):
  queryset = User.objects.all()
  serializer_class = UserSerializer

from snippets.serializers import UserSerializer

path('users/', views.UserList.as_view()),
path('users/<int:pk>/', views.UserDetail.as_view()),

def perform_create(self, serializer):
  serializer.save(owner=self.request.user)


owner = serializers.ReadOnlyField(source='owner.username')

from rest_framework import permissions

permissions_classes = (permissions.IsAuthenticatedOrReadOnly,)

from django.conf.urls import include

urlpatterns += {
  path('api-auth/', include('rest_framework.urls')),
}

from rest_framework import permissions
  """
  """
  def has_object_permission(self, request, view, obj):
    if request.method in permissions.SAFE_METHODS:
      return True
      
    return obj.owner == request.user

permission_classes = (permissions.IsAuthenticatedOrReadOnly,
  IsOwnerOrReadOnly,)
  
from snippets.permissions import IsOwnerOrReadOnly
```

```sh
http POST http://127.0.0.1:8000/snippets/ code="print(123)"
{
  "detail": "Authentication credentials were not provided."
}

http -a admin:password123 POST http://127.0.0.1:8000/snippets/ code="print(789)"

{
  "id": 1,
  "owner": "admin",
  "title": "foo",
  "code": "print(789)",
  "linenos": false,
  "language": "python",
  "style": "friendly"
}

rm -rf db.sqlite3
rm -r snippets/migrations
python manage.py makemigrations snippets
python manage.py migrate

python manage.py createsuperuser

http http://127.0.0.1:8000/shcema/ Accept:application/coreapi+json
pip install coreapi-cli
coreapi
coreapi get http://127.0.0.1:800/schema/
coreapi action snippets list
coreapi action snippets highlight --param id=1
coreapi credentials add 127.0.0.1 <username>:<password> --auth basic
coreapi reload
coreapi action snippets create --param title="Example" --param code="print('hello, world')"
coreapi action snippets delete --param id=7

pip install coreapi pyyaml

python manager.py runserver
pip install httpie

python manage.py shell

pip install djangorestframework
pip install markdown
pip install django-filter

virtualenv env
source env/bin/activate

pip install django
pip install djangorestframework
pip install pygments

cd ~
django-admin startproject tutorial
cd tutorial

python manage.py startapp snippets

python manage.py makemigrations snippets
python manage.py migrate
```

```
INSTALLED_APPS = (
  'rest_framework',
  'snippets.apps.SnippetsConfig',
)
```
