# HttpMessageConverter

## 참고
- [Spring 공식 문서 - Message Conversion](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-message-conversion)


## HttpMessageConverter 공식 문서 살펴보기

- HTTP 요청 및 응답의 본문을 `InputStream`/`OutputStrem` 을 통해 읽고 쓴다.
- 클라이언트 측(ex. RestTemplate) 과 서버 측(ex.Spring MVC REST 컨트롤러)에서 사용된다.
- 기본 미디어(MIME) 유형에 대한 구체적인 구현은 프레임워크에서 제공된다. 클라이언트측의 'RestTemplate'과 서버측의 'RequestMappingHandlerAdapter'에 등록된다.
- 구현체
  - StringHttpMessageConverter
  - FormHttpMessageConverter
  - ByteArrayHttpMessageConverter
  - MarshallingHttpMessageConverter
  - MappingJackson2HttpMessageConverter
  - MappingJackson2XmlHttpMessageConverter
  - SourceHttpMessageConverter
  - BufferedImageHttpMessageConverter


| 구현체                                   | media types                       | Content-Type             |
|----------------------------------------|-----------------------------------|--------------------------|
| StringHttpMessageConverter             |  text/*                           | text/plain               |
| FormHttpMessageConverter               | application/x-www-form-urlencoded |                          |
| ByteArrayHttpMessageConverter          | \*/* (all media types)            | application/octet-stream |
| MarshallingHttpMessageConverter        | text/xml, application/xml         |                          |
| MappingJackson2HttpMessageConverter    | application/json                  |                          |
| MappingJackson2XmlHttpMessageConverter | application/xml                   |                          |
| SourceHttpMessageConverter             | text/xml, application/xml         |                          |
| BufferedImageHttpMessageConverter      | supported by the Java I/O API     |                          |

### FormHttpMessageConverter

- form 데이터 를 읽고 씀
- `application/x-www-form-urlencoded`
- form 데이터를 읽어 `MultiValueMap<String, String>` 에 기록
- `MultiValueMap<String, Object>` 에서 읽은 multipart data 를 씀 (읽기는 불가)
- multipart/form-data 지원
- multipart subtypes 지원 (Spring Framework 5.2 부터)
- 자세한 내용은 javadoc 확인

### ByteArrayHttpMessageConverter

- 바이트 배열을 읽고 씀
- 모든 미디어 유형 지원 (\*/*)
- Content-Type : `application/octet-stream`

### MarshallingHttpMessageConverter

- XML 지원
- 이 Converter 를 사용하려면 Marshaller / Unmarshaller 필요
- `text/xml`, `application/xml`


### MappingJackson2HttpMessageConverter

- `application/json`
- `Jackson`의 `ObjectMapper` 를 이용하여 `JSON` 을 읽고 씀
- Jackson 이 제공하는 어노테이션으로 JSON mapping 을 재정의 할 수 있음
- 특정 유형에 따라 직렬화/역직렬화를 사용자 재정의 해야하는 경우, ObjectMapper 속성을 통해서 사용자 재정의 ObjectMapper 를 주입할 수 있음

### MappingJackson2XmlHttpMessageConverter

- `application/xml`
- Jackson XML extension 의 XmlMapper 를 이용하여 XML 을 읽고 씀

### SourceHttpMessageConverter

- `text/xml`, `application/xml`
- `javax.xml.transform.Source` 를 읽고 씀
- DOMSource, SAXSource, StreamSource 만 지원

### BufferedImageHttpMessageConverter

- 요청에서 `java.awt.image.BufferedImage`를 읽고 씀
- Java I/O API 에서 지원하는 미디어 유형을 읽고 씀

