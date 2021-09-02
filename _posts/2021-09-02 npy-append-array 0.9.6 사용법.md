---
title: "[대용량 이미지 처리] npy-append-array를 이용한 빠른 속도의 numpy stack"
date: 2021-09-02 13:30:00 -0400
categories: 
- 이미지분석
- ImageNet
- numpy
- 대용량이미지
- 딥러닝
- npy-append-array
---

지금까지는 for문을 이용해 numpy를 vstack, concatenate 하는 등의 방식으로 stack 후 저장하는 방식을 이용했습니다.

이 방법을 알고난 이후, 더 이상 그런 방법을 사용할 필요가 없어졌습니다.

with open 방식처럼, 로컬에 파일을 하나 만들어두고 그 파일에 append하여 stack하는 방법을 사용해, 수가 늘어나도 속도가 줄어들지 않습니다.

지금부터 간략히 코드를 통해 설명드리겠습니다.

# 패키지 설치

'''
pip install npy-append-array
'''

npy-append-array 패키지를 설치하고, document에 나와있는대로 따라하기만 하면 끝입니다.

# numpy 파일 저장

'''
from npy_append_array import NpyAppendArray
import numpy as np

arr1 = np.array([[1,2],[3,4]])
arr2 = np.array([[1,2],[3,4],[5,6]])

filename='out.npy'

np.save(filename, arr1)
'''

# numpy 파일 불러온 후 append

'''
npaa = NpyAppendArray(filename)
npaa.append(arr1)
npaa.append(arr2)
npaa.append(arr2)
'''

# 저장한 파일 불러와서 출력

'''
data = np.load(filename, mmap_mode="r")

print(data)
'''

# 전체 코드
'''
from npy_append_array import NpyAppendArray
import numpy as np

arr1 = np.array([[1,2],[3,4]])
arr2 = np.array([[1,2],[3,4],[5,6]])

filename='out.npy'

np.save(filename, arr1)

npaa = NpyAppendArray(filename)
npaa.append(arr1)
npaa.append(arr2)
npaa.append(arr2)

data = np.load(filename, mmap_mode="r")

print(data)
'''

# 다른 예시

저는 cifar10 데이터를 이용해 위의 방식을 활용하여 데이터를 저장해봤습니다.

위에서는 최초 numpy 파일을 저장하기 위해 arr1이라는 array를 저장했지만, 저는 np.zeros를 활용해 array를 만들어주었습니다.

'resize_img'는 제가 만든 함수로, 이 문서를 이해하는 데에는 필요하지 않습니다.

'''
filename = 'crop_cifar10_train.npy'

np.save(filename, np.zeros([1, 32, 32, 3]))
savearray = NpyAppendArray(filename)
train_empty = []

for idx, item in tqdm(enumerate(train_x), total = train_x.shape[0]):
    crops = resize_img(item)
    if crops == 'continue':
        train_empty.append(idx)
        continue
    else:
        savearray.append(crops)

train_data = np.load(filename, mmap_mode="r")
print(train_data.shape)
'''
