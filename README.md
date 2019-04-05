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
```

```sh
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
