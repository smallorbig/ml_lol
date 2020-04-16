---
layout: post
title: "FastText"
date: 2020-04-09
use_math: true
author: Oh

---

# Introduction



python에서 gensim 패키지를 사용해서 FastText를 사용해서 이것저것 살펴보자.

참고 : [An introduction of FastText](https://byeongkijeong.github.io/fastText/)

# Gensim

``` python
from gensim.models import FastText
sentences = [["This", "is", "an", "apple"], ["This", "is","a", "pen"]]

model = FastText(sentences, min_count=1)
is_vector = model['is']
apples_vector = model['apples']
```

미리 훈련된 모델 불러오기

```python
from gensim.models import FastText

model = FastText.load_fasttext_format('./wiki.ko.bin')

print(model.most_similar('대학교'))
# Output = 
#[('대학교예일', 0.8899640440940857),
# ('대학교을', 0.875703752040863),
# ('대학교하버드', 0.8646291494369507),
# ('대학교코넬', 0.8616819381713867),
# ('대학교별', 0.8541648387908936),
# ('대학교프린스턴', 0.8443434238433838),
# ('대학교시카고', 0.8431099653244019),
# ('대학교과', 0.839390754699707),
# ('대학교내', 0.837873101234436),
# ('대학교으로', 0.8376458883285522)]
```



# 문장 분류

참고 : [Facebook AI Research 토론](https://www.facebook.com/groups/1174547215919768)

![문장분류](/Users/osmfirst/TEMP/nlp/fasttext/ft_cls.jpg)

