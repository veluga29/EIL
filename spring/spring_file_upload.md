## HTML Form 전송 방식 차이
- `application/x-www-form-urlencoded`
	- HTML 폼 기본 전송 방식
	- 폼 태그에 `enctype` 옵션을 주지 않을 시 자동 지정
- **`multipart/form-data`**
	![multipart form http message](../images/multipart_form_http_message.png)
	- **여러 데이터 형식**을 함께 보내기 위한 Form 데이터 전송 방식 (HTTP 제공)
		- 파일은 문자가 아닌 **바이너리 타입**으로 전송 필요
		- 각각의 항목을 **구분**해 **한번에 전송**
			- e.g. 폼 데이터 전송 시 **문자와 바이너리를 동시 전송**
				- 문자: 이름, 나이...
				- 파일: 첨부파일
	- 폼 태그 -> `enctype="multipart/form-data"`
## 서블릿 파일 업로드
- `HttpServletRequest`
	- `request.getParameter(...)`
		- 요청 파라미터 접근
	- `request.getParts()`
		- `multipart/form-data` 전송 방식에서 각각 나누어진 부분을 받아서 확인
		- 개별 `Part` 메서드
			- `part.getSubmittedFileName()` : 클라이언트가 전달한 파일명
			- `part.getInputStream():` Part의 전송 데이터를 읽기 (Body)
			- `part.write(fullPath):` Part를 통해 전송된 데이터를 지정 경로에 저장
## 스프링 파일 업로드
- 업로드하는 HTML Form의 name에 맞추어 **`@RequestParam` 을 적용하면 됨**
	- `@RequestParam String itemName`
	- `@RequestParam MultipartFile file`
		- **`MultipartFile`** 인터페이스 제공
			- 메서드
				- `file.getOriginalFilename()` : 업로드 파일 명
				- `file.transferTo(...)` : 파일 저장
		- 서블릿에 비해
			- **`HttpServletRequest`를 사용 X**
			- **파일 부분만 구분**하기도 편리
- 예시 코드
	```java
	@Slf4j
	@Controller
	@RequestMapping("/spring")
	public class SpringUploadController {
	    
	    @Value("${file.dir}")
	    private String fileDir;
	    
	    @GetMapping("/upload")
	    public String newFile() {
	        return "upload-form";
		}
		
	    @PostMapping("/upload")
	    public String saveFile(@RequestParam String itemName,
	                           @RequestParam MultipartFile file, HttpServletRequest request) throws IOException {
	
			log.info("request={}", request);
	        log.info("itemName={}", itemName);
	        log.info("multipartFile={}", file);
	
			if (!file.isEmpty()) {
				String fullPath = fileDir + file.getOriginalFilename(); 
				log.info("파일 저장 fullPath={}", fullPath); 
				file.transferTo(new File(fullPath));
			}
	        
	        return "upload-form";
	    }
	}
	```
## 멀티파트 관련 사용 옵션 (`application.properties`)
- 실제 파일 저장 경로 지정
	- `file.dir=파일 업로드 경로`
		- e.g. `/Users/lucian/study/file/`
		- 해당 경로에 **반드시 실제 폴더 만들어두기**
	- 지정 파일 경로는 컨트롤러의 멤버 변수에 주입해 사용 가능
		```java
		//application.properties에서 설정한 파일 경로 주입
		@Value("${file.dir}")
		private String fileDir;
		```
- 업로드 사이즈 제한
	```yaml
	//파일 하나의 최대 사이즈 (기본 1MB)
	spring.servlet.multipart.max-file-size=1MB
	//멀티 파트 요청 하나의 여러 파일 전체 합 (기본 10MB)
	spring.servlet.multipart.max-request-size=10MB
	```
	- 큰 파일 무제한 업로드를 예방하고 업로드 사이즈 제한 가능
	- 사이즈를 넘길 시 예외 발생 (`SizeLimitExceededException`)
- 서블릿 컨테이너 멀티파트 관련 처리 옵션
	- `spring.servlet.multipart.enabled` (기본 `true`)
		- `false`: 멀티파트 처리 안하기
			- 결과
				```java
				request=org.apache.catalina.connector.RequestFacade@xxx
				itemName=null
				parts=[]
				```
		- `true`: 멀티파트 처리하기
			- 결과
				```java
				request=org.springframework.web.multipart.support.StandardMultipartHttpServletRequest
				itemName=Spring
				parts=[ApplicationPart1, ApplicationPart2]
				```
			- `true`일 시, 스프링 `DispatcherServlet`의 **`MultipartResolver`를 실행**
				- `MultipartResolver`는 멀티파트 요청이 온 경우
				  `HttpServletRequest` -> **`MultipartHttpServletRequest` 변환**
			- 스프링 기본 멀티파트 리졸버는 **`StandardMultipartHttpServletRequest` 반환**
				- `MultipartHttpServletRequest`
					- `HttpServletRequest`의 **자식 인터페이스**
					- 멀티파트 관련 추가 기능 제공
				- `StandardMultipartHttpServletRequest`
					- `MultipartHttpServletRequest` 인터페이스 **구현체**
## 실제 파일 업로드 구현 시 주의사항
- **고객이 업로드한 파일명**과 **서버 내부 관리 파일명**은 다르게 할 것
	- 서로 다른 고객이 같은 파일 이름을 업로드하면 **기존 파일과 충돌 발생**
	- 예시 구현
		- 업로드 파일 정보
			```java
			@Data
			public class UploadFile {
			    
			    private String uploadFileName;
			    private String storeFileName;
			    
			    public UploadFile(String uploadFileName, String storeFileName) {
			        this.uploadFileName = uploadFileName;
			        this.storeFileName = storeFileName;
			    } 
			}
			```
		- 상품 도메인 객체
			```java
			@Data
			 public class Item {
			     private Long id;
			     private String itemName;
			     private UploadFile attachFile;
			     private List<UploadFile> imageFiles;
			}
			```
- 파일 저장 객체 구현하기
	- **멀티파트 파일**을 **서버에 저장**하는 역할
	- 파일은 보통 **로컬 스토리지**나 **S3에 저장**하고 **DB에는 해당 경로만 저장** (DB에 파일 자체 저장 X)
	- 예시 구현
		```java
		@Component
		public class FileStore {
		
			@Value("${file.dir}")
			private String fileDir;
		    
		    public String getFullPath(String filename) {
		        return fileDir + filename;
			}
			
		    public List<UploadFile> storeFiles(List<MultipartFile> multipartFiles) throws IOException {
		        
		        List<UploadFile> storeFileResult = new ArrayList<>();
		        for (MultipartFile multipartFile : multipartFiles) {
		            if (!multipartFile.isEmpty()) {
		                storeFileResult.add(storeFile(multipartFile));
					}
				}
		        
		        return storeFileResult;
		    }
		    
		    public UploadFile storeFile(MultipartFile multipartFile) throws IOException {
		        
		        if (multipartFile.isEmpty()) {
		            return null;
				}
		        
		        String originalFilename = multipartFile.getOriginalFilename();
		        String storeFileName = createStoreFileName(originalFilename);
		        multipartFile.transferTo(new File(getFullPath(storeFileName)));
		        return new UploadFile(originalFilename, storeFileName);
			}
			
		    //서버 내부 관리 파일명 생성 (UUID 사용해 충돌 방지)
		    private String createStoreFileName(String originalFilename) {
		        String ext = extractExt(originalFilename);
		        String uuid = UUID.randomUUID().toString();
		        return uuid + "." + ext;
			}
		
		    //확장자 추출 함수
		    private String extractExt(String originalFilename) {
		        int pos = originalFilename.lastIndexOf(".");
		        return originalFilename.substring(pos + 1);
			}
		}
		```
- 파일 저장 폼 전송 객체 예시
	```java
	@Data
	public class ItemForm {
	    private Long itemId;
	    private String itemName;
	    private List<MultipartFile> imageFiles; //이미지 다중 업로드
	    private MultipartFile attachFile;
	}
	```
- 파일 저장 뷰 예시
	- 다중 파일 업로드는 `<input>` 태그에 `multiple="multiple"` 옵션 지정
	- `ItemForm`의 `List<MultipartFile> imageFiles`을 통해 **여러 이미지 파일** 받을 수 있음
	```html
	<!DOCTYPE HTML>
	<html xmlns:th="http://www.thymeleaf.org">
	<head>
	    <meta charset="utf-8">
	</head>
	<body>
	<div class="container">
		<div class="py-5 text-center">
			<h2>상품 등록</h2>
		</div>
	    <form th:action method="post" enctype="multipart/form-data">
	        <ul>
				<li>상품명 <input type="text" name="itemName"></li> 
				<li>첨부파일<input type="file" name="attachFile" ></li> 
				<li>이미지 파일들<input type="file" multiple="multiple" name="imageFiles" ></li>
	        </ul>
	        <input type="submit"/>
	    </form>
	</div> <!-- /container -->
	</body>
	</html>
	```
- 파일 조회 및 다운로드 예시
	- 이미지 조회
		- `UrlResource`로 이미지 파일을 읽어서 `@ResponseBody`로 **이미지 바이너리 반환**
	- 파일 다운로드
		- **`Content-Disposition`** 헤더에 **`attachment; filename="업로드 파일명"`** 주기
		- 파일 다운로드 시에는 **고객이 업로드한 파일명**으로 다운로드하는게 좋음 (UTF_8 인코딩)
		- `UrlResource`로 파일을 읽어서 헤더와 바디를 `ResponseEntity<Resource>` 반환
	- 예시 구현
		```java
		@Slf4j
		@Controller
		@RequiredArgsConstructor
		public class ItemController {
		
		    private final ItemRepository itemRepository;
		    private final FileStore fileStore;
		    
		    @GetMapping("/items/new")
		    public String newItem(@ModelAttribute ItemForm form) {
		        return "item-form";
		    }
		    
		    @PostMapping("/items/new")
		    public String saveItem(@ModelAttribute ItemForm form, RedirectAttributes redirectAttributes) throws IOException {
		        
		        UploadFile attachFile = fileStore.storeFile(form.getAttachFile());
		        List<UploadFile> storeImageFiles = fileStore.storeFiles(form.getImageFiles());
		
				//데이터베이스에 저장
				Item item = new Item(); 
				item.setItemName(form.getItemName()); 
				item.setAttachFile(attachFile); 
				item.setImageFiles(storeImageFiles); 
				itemRepository.save(item);
		        redirectAttributes.addAttribute("itemId", item.getId());
		        return "redirect:/items/{itemId}";
			}
			
		    @GetMapping("/items/{id}")
		    public String items(@PathVariable Long id, Model model) {
		        Item item = itemRepository.findById(id);
		        model.addAttribute("item", item);
			    return "item-view";
		    }
		    
		    @ResponseBody
		    @GetMapping("/images/{filename}")
		    public Resource downloadImage(@PathVariable String filename) throws MalformedURLException {
		        return new UrlResource("file:" + fileStore.getFullPath(filename));
		    }
		    
		    @GetMapping("/attach/{itemId}")
		    public ResponseEntity<Resource> downloadAttach(@PathVariable Long itemId) throws MalformedURLException {
		        
		        Item item = itemRepository.findById(itemId);
		        String storeFileName = item.getAttachFile().getStoreFileName();
		        String uploadFileName = item.getAttachFile().getUploadFileName();
		        
		        UrlResource resource = new UrlResource("file:" + fileStore.getFullPath(storeFileName));
		        
		        log.info("uploadFileName={}", uploadFileName);
		        String encodedUploadFileName = UriUtils.encode(uploadFileName,
		StandardCharsets.UTF_8);
		        String contentDisposition = "attachment; filename=\"" +
		encodedUploadFileName + "\"";
		        return ResponseEntity.ok()
		                .header(HttpHeaders.CONTENT_DISPOSITION, contentDisposition)
		                .body(resource);
			}
		}
		```