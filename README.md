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

```

```sh
pip install djangorestframework
pip install markdown
pip install django-filter
```

```
```
