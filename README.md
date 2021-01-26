# DjangoV1
DjangoLogin
  
IMPLEMENTATION ENGINEER INTERVIEW QUESTIONS 
 
Please answer all the questions in SECTION A and one question of your choice in SECTION B. 
SECTION A 
1.	Give examples of different integration protocols you have come across and give example scripts in python 3 on how to achieve each one.  (10 pts) 
API, Webhooks, Integration service Component (ISC).
a)	Start Django Project
b)	Setup database migrations
python3 manage.py makemigrations 
python3 manage.py migrate
c)	Create our first user
python3 manage.py createsuperuser
Username: admin
Email address: testlucy51@gmail.com
Password: ******** 
Password (again): ******** 
Superuser created successfully.
d)	Build api using Django Rest Framework
pip3 install djangorestframework
cd rapidapi/api && touch serializers.py
serializers.py code
from django.contrib.auth.models import User, Group
from rest_framework import serializers
class UserSerializer(serializers.HyperlinkedModelSerializer):
class Meta:
model = User
fields = ['url', 'username', 'email', 'groups']
class GroupSerializer(serializers.HyperlinkedModelSerializer):
class Meta:
model = Group
fields = ['url', 'name']

views.py code
from django.contrib.auth.models import User, Group
from rest_framework import viewsets
from .serializers import UserSerializer, GroupSerializer
class UserViewSet(viewsets.ModelViewSet):
"""
API endpoint that allows users to be viewed or edited.
"""
queryset = User.objects.all().order_by('-date_joined')
serializer_class = UserSerializer
class GroupViewSet(viewsets.ModelViewSet):
"""
API endpoint that allows groups to be viewed or edited.
"""
queryset = Group.objects.all()
serializer_class = GroupSerializer

CRUD OPERATIONS
urls.py code
from django.urls import include, path
from rest_framework import routers
from rapidapi.api import views
router = routers.DefaultRouter()
router.register(r'users', views.UserViewSet)
router.register(r'groups', views.GroupViewSet)
# Setup automatic URL routing
# include login URLs for the browsable API.
urlpatterns = [
path('', include(router.urls)),
path('api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
Pagination(settings.py)
REST_FRAMEWORK = {
'DEFAULT_PAGINATION_CLASS': 
'rest_framework.pagination.PageNumberPagination',
'PAGE_SIZE': 10
}

Installed Apps(settings.py)
INSTALLED_APPS = [
‘django.contrib.admin’,
‘django.contrib.auth’,
‘django.contrib.contenttypes’, 
‘django.contrib.sessions’,
‘django.contrib.messages’,
‘django.contrib.staticfiles’,
‘api’,
‘rest_framework’,
]

2.	Give a walkthrough of how you will manage a data streaming application sending one million notifications every hour while giving examples of technologies and configurations you will use to manage load and asynchronous services. (10 pts)
a)	Notification Server – It exposes an API internally to send notifications. The API accepts the user ID, optional application ID as HTTP headers and notification information in the request body. The notification server abstracts the external interface to the system.
E.g. of uber app
POST http://<base_url>/notification
user_id: <user_id>
application_id: <application_id>{
	"payload": {},
	"title": "You driver is here",
	"message": "Please meet your driver at the pickup point"
}

b)	Token Store – when a user login into the application makes a call to the token store API with the device token and application ID therefore, the token store abstracts the process of deciding which devices to send a notification to a user.
c)	Job Queues – Used to isolate failures for each application and provider type. E.g., RabbitMQ can be used as an exchange of job queues. A queue is for each application ID and provider type.
d)	Notification workers – The worker processes consume messages from the job queues and send out messages to the respective notification providers. To accommodate different service providers, we use interfaces to abstract the functionality implemented by each provider. The notification worker abstracts the process of selecting the correct provider to send the notification.
type PushService interface {
Push(ctx context.Context, m PushRequest) (PushResponse, error)
}
type PushRequest struct {
   DeviceID string 
   Title string
   Message string
   Payload map[string]interface{}
}
type PushResponse struct {
Success bool
ErrorMsg string
}
type FCMProvider struct {
	// configuration for our provider, like API token and URL endpoint
}func (p *FCMProvider) Push(ctx context.Context, m queue.Message) (notification.PushResponse, error) {
	// code to send a notification payload to FCMs server
}type APNSProvider struct {
	// configuration for our provider, like API token and URL endpoint
}func (p *APNSProvider) Push(ctx context.Context, m queue.Message) (notification.PushResponse, error) {
	// code to send a notification payload to FCMs server
}

3.	Give examples of different encryption/hashing methods you have come across (one way and two way) and give example scripts in python 3 on how to achieve each one. (20 pts) 
a)	MD5 – Hash function that accepts sequence of bytes and returns 128bit hash value. Used to check for data Integrity.
Byte - Byte
import hashlib 
result = hashlib.md5(b'InterIntel') 
print ("The byte equivalent of hash is: ", end ="") 
print(result.digest()) 


String-Hexadecimal
import hashlib 
  
//initializing string 
str2hash = "InterIntel"

result = hashlib.md5(str2hash.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of hash is : ", end ="") 
print(result.hexdigest()) 

b)	SHA – are set of cryptographic hash functions defined by the language to be used for various applications such as password security. They include SHA-1 , SHA-256, SHA-384, SHA-224 and SHA-512.
Supported by python in hashlib
import hashlib 
//prints all available algorithms 
print ("The available algorithms are : ", end ="") 
print (hashlib.algorithms_guaranteed) 

SHA hash Algorithms
import hashlib 
  
//initializing string 
str = "InterIntel"
  
//encoding InterIntel using encode() 
//then sending to SHA256() 
result = hashlib.sha256(str.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of SHA256 is : ") 
print(result.hexdigest()) 
  
print ("\r") 
  
//initializing string 
str = " InterIntel "
  
//encoding InterIntel using encode() 
//then sending to SHA384() 
result = hashlib.sha384(str.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of SHA384 is : ") 
print(result.hexdigest()) 
  
print ("\r") 
  
//initializing string 
str = " InterIntel "
  
//encoding InterIntel using encode() 
//then sending to SHA224() 
result = hashlib.sha224(str.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of SHA224 is : ") 
print(result.hexdigest()) 
  
print ("\r") 
  
//initializing string 
str = " InterIntel "
  
//encoding InterIntel using encode() 
//then sending to SHA512() 
result = hashlib.sha512(str.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of SHA512 is : ") 
print(result.hexdigest()) 
  
print ("\r") 
  
//initializing string 
str = " InterIntel "
  
//encoding InterIntel using encode() 
//then sending to SHA1() 
result = hashlib.sha1(str.encode()) 
  
//printing the equivalent hexadecimal value. 
print("The hexadecimal equivalent of SHA1 is : ") 
print(result.hexdigest()) 

c)	NTLM - This is the NT LAN Manager algorithm. The NTLM algorithm is used for password hashing during authentication.
import hashlib,binascii
hash = hashlib.new('md4', "thisismyhashvalue".encode('utf-16le')).digest()
print binascii.hexlify(hash)
d)	LANMAN - Microsoft LANMAN is the Microsoft LAN Manager hashing algorithm. LANMAN was used by legacy Windows systems to store passwords. 
From passlib.hash import lmhash
//hash password
h = lmhash.hash(“password”)
h
‘e52cac67419a9a224a3b108f3ffa6cb6d’
//verify correct password
lmhash.verify(“password”,  h)
True
//verify incorrect password
lmhash.verify(“secret” ,  h)
False
SECTION B 
1.	Create a login and a success page in Django. A mockup of the created pages should also be submitted. The mockups should have been created by using advanced design/wireframe tools thus showcasing prowess in usage of the tools and use of production server deployments on uwsgi/nginx. Ensure that the sessions are well and securely managed. (60 pts) 
Mockups
Link to the mockups
https://app.moqups.com/KQQnZsIhmK/view/page/ad64222d5?ui=0&fit_width=1
