# 문장 요약을 위한 웹 크롤러
- boostcamp ai tech 최종 프로젝트 학습을 위한 텍스트 데이터 크롤링
## Medium Crawler
### 👀 How to
1. `get_links` 내부의 `path` 를 chromedriver의 위치로 설정 
2. `queries`에 크롤링 할 검색어를 리스트로 저장
3. `to_csv(저장될 파일명)` 지정 
> *[NOTE] 아직 Read More 은 사용하지 않음. 추후 개선할 예정*

<br>

## 🚀 Troubleshooting
### ✔️ `get_links`에서 URL을 제대로 받아오지 못하는 문제 
```python
href = urllib.parse.quote(a['href'], safe=':/?&=')  # URL 인코딩
full_url = "https://medium.com" + href
```
- medium의 웹문서를 확인하여 `aria-label="Post Preview Title"` 의 속성을 가지는 `<a>`의 `href` 속성값으로 접근함
- 해당 값을 그대로 사용할 수 없음. 누락된 `https://medium.com` 을 기존 `href`에 추가하여 링크를 얻음 
- 이 과정에서 인코딩이 제대로 되지 않아 에러가 발생하였고 URL 인코딩을 통해 이를 해결함.


### ✔️ `get_stories`에서 본문을 제대로 받아오지 못하는 문제 
```python
def get_stories(links):
    stories = []
    for index, link in enumerate(links):
        try:
            print(f'link: {link}')
            soup = get_soup(link)
            p_tags = soup.find_all('p', class_=lambda value: value and 'pw-post-body-paragraph' in value.split())
            print(p_tags)
            story = ' '.join([p.get_text(strip=True) for p in p_tags])
            print('Story:', story)
            stories.append(story)
            print(index, 'of', len(links), 'stories')
        except:
            pass
    return stories
```
- `<p>` 태그의 class 명을 제대로 지정하지 않아서 발생한 문제
- medium 에서는 본문 클래스 명이 포스트마다 다르지만 클래스 명에 `pw-post-body-paragraph` 가 포함되는 경우 본문에 해당됨을 확인하고 위와 같이 코드 수정 