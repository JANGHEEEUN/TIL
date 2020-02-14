# Django

### admin site에 model 객체 등록하기

---

##### 1. admin site를 위한 username & user pw 생성

- cmd에서 `manage.py`를 통해 생성

  `class`: DB table | `Class's variable`: Column(DB field) | `Class's object`: Row(DB record)
  
- 만들고자 하는 DB table 

  <Class GuessNumbers>

  | id(pk)<br />자동 생성 | name<br />리스트의 이름 | text<br />리스트설명 | lottos<br />generate로 생성되는 <br />번호 리스트 | num_lotto<br />generate 함수를 통해<br />생성할번호 set 수 | update_date<br />생성 일자 |
  | :-------------------: | :---------------------: | :------------------: | :-----------------------------------------------: | :--------------------------------------------------------: | :------------------------: |
  |           1           |       num_list_1        |       1st set        |               [[1,4,5,8,12,26]...]                |                             5                              |        2019.44.44.         |
  |                       |                         |                      |                                                   |                                                            |                            |

- models.py code

  ```python
  from django.db import models
  from django.utils import timezone
  import random
  
  class GuessNumbers(models.Model):
      name = models.CharField(max_length=24)
      text = models.CharField(max_length=255)
      lottos = models.CharField(max_length=255, default='[1,2,3,4,5,6]')
      num_lotto = models.IntegerField(default=5) # 6개 번호 set 수
      update_date = models.DateTimeField()
  
      def generate(self):
          self.lottos=""
          origin = list(range(1,46))
  
          for _ in range(0, self.num_lotto):
              random.shuffle(origin)
              guess = origin[:6]
              guess.sort()
              self.lottos += str(guess) + '\n'
          self.update_date = timezone.now()
          self.save()
  
      def __str__(self):
          return 'pk {} : {} - {}'.format(self.pk, self.name, self.text)
  ```

- `models.py` 변경 후에 DB에 반영해주어야 함 (실제 물리 DB: db.sqlite3)

  - models.py에서의 변화를 모아서 migration file로 구성 = `commit`

    ```bash
    python manage.py makemigrations
    ```

  - migration file을 바탕으로 실제 DB Schema 변경 = `push`

    ```
    python manage.py migrate
    ```