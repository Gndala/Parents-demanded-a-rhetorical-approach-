الإعداد (مرة واحدة، مجاني):
1) Firebase: مشروع جديد ← Firestore Database ← Authentication (Email/Password).
2) Authentication ← Users ← حساب المدير بنفس الإيميل المكتوب في ADMIN داخل admin.html.
3) الصق firebaseConfig مكان FB في الملفات الثلاثة (parent / admin / teacher).
4) Firestore ← Rules ← الصق التالي ثم Publish:

rules_version='2';
service cloud.firestore{match /databases/{db}/documents{
 function adm(){return request.auth!=null&&request.auth.token.email=='a7qubd85hw5r@gmail.com'}
 function td(){return get(/databases/$(db)/documents/teachers/$(request.auth.uid)).data}
 function tch(){return request.auth!=null&&exists(/databases/$(db)/documents/teachers/$(request.auth.uid))&&td().ok==true}
 function staff(){return adm()||tch()}
 function mine(){return (resource.data.grade+'|'+resource.data.section) in td().get('cls',[td().grade+'|'+td().section])}
 function op(f){return !(exists(/databases/$(db)/documents/pub/_cfg)&&get(/databases/$(db)/documents/pub/_cfg).data.get(f,false)==true)}
 function ch(){return request.resource.data.diff(resource.data).affectedKeys()}
 match /students/{id}{
  allow get:if true;
  allow list:if staff()||resource.data.claim==true;
  allow create:if adm()||(op('regP')&&request.resource.data.keys().hasOnly(['name','parent','phone','grade','section','photo','g','b','abs','warn','obj','created'])&&request.resource.data.g.size()==0&&request.resource.data.abs.size()==0&&request.resource.data.warn.size()==0&&request.resource.data.obj.size()==0&&request.resource.data.photo.size()<100000&&request.resource.data.name.size()<100);
  allow update:if adm()
   ||(tch()&&mine()&&ch().hasOnly(['g','b','obj']))
   ||(ch().hasOnly(['obj'])&&request.resource.data.obj.size()==resource.data.obj.size()+1)
   ||(op('regP')&&resource.data.claim==true&&request.resource.data.claim==false&&ch().hasOnly(['parent','phone','photo','claim'])&&request.resource.data.photo.size()<100000);
  allow delete:if adm();}
 match /pub/{id}{allow read:if true;allow write:if adm();}
 match /support/{id}{allow create,read,delete:if adm();}
 match /teachers/{id}{allow create:if request.auth!=null&&request.auth.uid==id&&request.resource.data.ok==false&&op('regT');
  allow read:if adm()||(request.auth!=null&&request.auth.uid==id);allow update,delete:if adm();}
}}

5) ارفع كل الملفات التسعة (index / parent / admin / teacher / logo.png / sw.js / manifest-parent / manifest-admin / manifest-teacher) بنفس المجلد على GitHub Pages، وأضف النطاق gndala.github.io في Authentication ← Settings ← Authorized domains.
