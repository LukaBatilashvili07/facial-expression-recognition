# Facial Expression Recognition Challenge
## პროექტის მიმოხილვა

FER2013 - დავალება არის 48x48 grayscale სახის სურათებიდან 7 ემოციის კლასიფიკაცია.

## ექსპერიმენტების სტრუქტურა
### V1 - Baseline CNN
underfitting-ის დემონსტრირება

| ექსპერიმენტი | Learning Rate | Val Accuracy | სტატუსი |
| exp1 | 1e-3 | 43% | Underfitted |
| exp2 | 1e-4 | 42% | Underfitted |

მოდელს არ აქვს საკმარისი capacity. ტრენინგ და ვალიდაციის accuracy თითქმის ტოლია, მაგრამ ორივე დაბალი. ეს ნიშნავს, რომ მოდელი ვერ სწავლობს საკმარისად.

### V2 - CNN, BatchNorm და Dropout

| ექსპერიმენტი | LR | Dropout | Val Acc | Gap | ანალიზი |
| exp1 | 1e-3 | 0.5 | 61.9% | 0.6% | კარგი |
| exp2 | 1e-4 | 0.5 | 60.9% | -1.4% | ნელი |
| exp3 | 1e-3 | 0.3 | 62.6% | 1.3% | კარგი |
| exp4 | 5e-4 | 0.25 | 64.02% | 1.1% | საუკეთესო |

**V2 exp4:**
- 4 Conv ფენა (32, 64, 128, 256 channels)
- BatchNormalization ყოველი Conv ფენის შემდეგ
- Dropout=0.25 ოპტიმალური რეგულარიზაცია
- ტრენინგ-ვალიდაციის gap = 1.1%

### V3 - ResNet18 Transfer Learning

| მოდელი | Val Accuracy | სტატუსი |
| ResNet18 (Fixed) | 59.35% | ვერ გამოთქმა |

ImageNet RGB სურათები და grayscale გამომეტყველებები ძალიან განსხვავებულია. Transfer learning-მა არ გააუმჯობესა ფორმა.

## საბოლოო შედეგების შედარება
| მოდელი | Best Val Acc | Train Acc | Gap | სტატუსი |
| V1 exp1 | 43% | 45% | 2% | Underfitted |
| V1 exp2 | 42% | 39% | 3% | Underfitted |
| V2 exp1 | 61.9% | 62.5% | 0.6% | კარგი |
| V2 exp2 | 60.9% | 59.5% | -1.4% | კარგი |
| V2 exp3 | 62.6% | 63.9% | 1.3% | კარგი |
| V2 exp4 | 64.02% | 65.1% | 1.1% | საუკეთესო |
| V3 Fixed | 59.35% | 59% | 0.35% | Transfer Failed |

### 1. V1 - Underfitting დემონსტრაცია
დავიწყეთ პატარა 2-ფენიანი CNN-ით underfitting-ის ჩვენებით. ტრენინგის და ვალიდაციის accuracy თითქმის ტოლია, მაგრამ დაბალი (43%), რაც აჩვენებს რომ მოდელი ცუდად სწავლობს.

### 2. V2 - Capacity გაზრდა
4 Conv ფენამდე გავზარდეთ მოდელი და დავამატეთ BatchNormalization და Dropout. accuracy 43%-დან 62.6%-მდე გაიზარდა.

BatchNorm: ასტაბილიზებს ტრენინგს, უფრო მაღალი learning rate.
Dropout: ხელს უშლის overfitting-ს ნეირონების შემთხვევითი დეაქტივაციით ტრენინგის დროს.

### 3. V2 Hyperparameter Tuning
სხვადასხვა learning rate-ი და dropout მნიშვნელობა:
- LR 1e-3: ძალიან მაღალი, არასტაბილური
- LR 1e-4: ძალიან დაბალი
- LR 5e-4: ოპტიმალური
- Dropout 0.5
- Dropout 0.25

V2 exp4 (lr=5e-4, dropout=0.25) - 64.02% accuracy, gap = 1.1%.

### 4. V3 - Transfer Learning
ResNet18. grayscale გამომეტყველებებსა და ბუნებრივ სურათებს შორის gap ძალიან დიდია. 

მხოლოდ 59% accuracy.

## ანალიზი
### V2 exp4 საუკეთესო
**Learning Rate ანალიზი:**
- LR=1e-3: ძალიან მაღალი, არასტაბილური
- LR=1e-4: ძალიან დაბალი, ნელი სწავლა
- LR=5e-4: ოპტიმალური, სტაბილური და ჩქარი

**Dropout ანალიზი:**
- Dropout=0.5: ნეირონების ნახევარი დეაქტივირებულია
- Dropout=0.3: კარგი რეგულარიზაცია
- Dropout=0.25: 25% დეაქტივაცია, ოპტიმალური რეგულარიზაცია

**Overfitting vs Underfitting:**
- V1: Underfitted (ორივე დაბალი)
- V2: კარგი ბალანსი
- V2 exp4: საუკეთესო ბალანსი (1.1% gap)


## ტრენინგის მეთოდოლოგია
- Forward Check
- Backward Check
- Overfit 1 Batch Check
- WandB Logging

## ფაილები და ლინკები
- Repository: https://github.com/LukaBatilashvili07/facial-expression-recognition
- WandB Project: https://wandb.ai/lbati23-free-university-of-tbilisi-/fer-challenge-public