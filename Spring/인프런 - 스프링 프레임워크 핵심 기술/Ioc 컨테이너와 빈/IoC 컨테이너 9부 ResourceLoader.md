
# ResourceLoader

인터페이스 이름에서 알 수 있듯이 **리소스를 로딩해주는 인터페이스**이다.

ResourceLoader에 있는 **getResource() 메소드**를 사용하여 리소스를 읽어올 수 있다.

```
@Component
public class AppRunner implements ApplicationRunner {
    
 	@Autowired
 	ResourceLoader resourceLoader;
    	
   @Override
   public void run(ApplicationArguments args) throws Exception {
   
    	//getResource() 메소드를 사용하여 리소스를 읽어올 수 있다.
    	//classpath에 있는 test.txt 파일을 읽어온다.
    	Resource resource = resourceLoader.getResource("classpath:test.txt");
    		
    	//exists() 메소드는 해당 리소스가 존재하는지 판별해주는 메소드다.
    	System.out.println(resource.exists());
    		
    	//getDescription() 메소드는 해당 리소스가 어디에 있는지 대략적인 정보를 알려준다.
    	System.out.println(resource.getDescription());
    		
    	//파일의 내용을 읽어온다.
    	//readString() 메소드는 자바11에 생긴 기능이다.
    	System.out.println(Files.readString(Path.of(resource.getURI())));
    }	
}
```
