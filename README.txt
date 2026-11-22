الإعداد (مرة واحدة، مجاني):
1) console.firebase.google.com ← مشروع جديد ← Firestore Database (Create) ← Authentication ← Email/Password (Enable).
2) Authentication ← Users ← Add user: أنشئ حساب المدير بنفس الايميل المكتوب في ADMIN داخل admin.html.
3) Project settings ← Web app ← انسخ firebaseConfig والصقه مكان FB في الملفين parent.html و admin.html.
4) Firestore ← Rules ← الصق التالي ثم Publish:

rules_version='2';
service cloud.firestore{match /databases/{db}/documents{
 function adm(){return request.auth!=null&&request.auth.token.email=='a7qubd85hw5r@gmail.com'}
 function staff(){return adm()||(request.auth!=null&&get(/databases/$(db)/documents/teachers/$(request.auth.uid)).data.ok==true)}
 match /students/{id}{allow get,create:if true;allow list:if staff();
  allow update:if staff()||request.resource.data.diff(resource.data).affectedKeys().hasOnly(['obj']);allow delete:if staff();}
 match /pub/{id}{allow read:if true;allow write:if staff();}
 match /support/{id}{allow create:if staff();allow read,delete:if adm();}
 match /teachers/{id}{allow create:if request.auth!=null&&request.auth.uid==id;
  allow read:if adm()||(request.auth!=null&&request.auth.uid==id);allow update,delete:if adm();}
}}

5) ارفع كل الملفات (نفس المجلد) على استضافة مجانية: Firebase Hosting أو Netlify أو GitHub Pages (التثبيت كتطبيق يحتاج https).
