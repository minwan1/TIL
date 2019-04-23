# API
## XMLHttpRequest HTTP 통신 (API-1)
서버에 API요청을 하기위해서는 아래와같이 XMLHttpRequest는 객체를 이용해 HTTP 통신을 할 수 있다.


```javascript
      fetchData() {
        this.loading = true;
        const req = new XMLHttpRequest();
        req.open('GET', 'http://localhost:3000/health');
        req.send();

        req.addEventListener('load', () => {
          this.loading = false;
          this.apiRes = {
            status: req.status,
            statusText :req.statusText,
            response: JSON.parse(req.response)
          }
        });
      }
```

## Axios (API-2)

axios를 사용하면 아래와같이 http통신을 할 수 있다.

```javascript
      fetchData() {
        this.loading = true;

        axios.get('http://localhost:3000/health')
          .then(res =>{
            this.apiRes = res.data;
          })
          .catch(res =>{
            this.error = res.response.data;
          })
          .finally(res =>{
            this.loading = false;
          });
      }
```

## API 모듈화 (API-4)
위에서 axios를 바로 home 컴포넌트에서 사용하는데 이렇게되면 axios에 의존적이고 소스또한 많이 겹치게된다. 이러한문제를 해결하기위해서 api라는 폴더를 하나만들고 api호출부분을 아래와같이 모듈화 할 수 있다.


```javascript
import axios from 'axios'
import router from '../router'

const DOMAIN = 'http://localhost:3000';
const UNAUTHORIZED = 401;
const onUnauthororized = () =>{
  router.push('/login');
};



const request = (method, url, data) =>{
  return axios({
    method,
    url: DOMAIN + url,
    data
  }).then(res => res.data)
    .catch(res => {
      const {status} = res.response;
      if(status == UNAUTHORIZED) return onUnauthororized();
      throw Error(res)
    })
};

export const board = {
  fetch() {
    return request('get', '/boards')
  }
};


```