$postman-collection ishlatib shu loyiha uchun Postman collection yaratib ber

Shu loyiha uchun Postman collection yarat. Duplicate bo‘lmasin, baseUrl collection variable bo‘lsin, environment file bo‘lmasin.


!
postman/
├── README.md
└── <service-name>.postman_collection.json



Collection ichida:

har endpoint faqat 1 marta bo‘ladi
localhost, 127.0.0.1, server IP uchun alohida duplicate request qilinmaydi
IP almashtirish faqat baseUrl variable orqali bo‘ladi
environment .json fayllar qo‘shilmaydi
auth apiKey collection variable’da turadi
uploaddan qaytgan key, url keyingi requestlarga variable qilib saqlanadi



Standart variables:

baseUrl
apiKey
expiresIn
fileKey
downloadUrl



Standart request tartibi, Requestlar duplicate bo‘lmasin, lekin har endpoint bitta bo‘lsin:

Health Check
Upload File
Get Download Link
Download File Using Presigned URL



Validation qoidasi:

JSON valid bo‘lishi kerak
duplicate request bo‘lmasligi kerak
postman/ ichida faqat collection va README bo‘lishi kerak
imkon bo‘lsa flow real ishlatib ko‘riladi



Upload File response’dan keyingi requestlar uchun variable saqlansin.

Upload File response’dan:
key -> fileKey
url -> downloadUrl

Bular pm.collectionVariables.set orqali saqlanishi kerak.



Get Download Link ishlashi uchun fileKey bo‘sh bo‘lmasligi kerak.
Download File Using Presigned URL ishlashi uchun downloadUrl bo‘sh bo‘lmasligi kerak.
Agar bo‘sh bo‘lsa, avval Upload File requestini ishlatish kerak.




X-API-Key header collection-level auth bo‘ladi:
X-API-Key: {{apiKey}}

Health Check va Download File Using Presigned URL noauth bo‘ladi.