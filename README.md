# New Web App

Phát triển ứng dụng web sử dụng ReactJS và RESTFul Web Services.

# Công nghệ sử dụng
* [``Java 8``](https://www.java.com) - Ngôn ngữ phát triển lớp ``back-end``
* [``Maven 3+``](https://maven.apache.org/) - Quản lý các ``dependency`` sử dụng trong dự án
* [``Spring Framework``](https://spring.io/) - Sử dụng phát triển lớp ``back-end``
    * Spring Boot
    * Spring Web
    * Spring Security
    * Spring Data JPA
* [``MapStruct``](http://mapstruct.org/) - Sử dụng mapping ``dto-entity-dto``
* [``ReactJS``](https://reactjs.org/) - Sử dụng phát triển lớp ``front-end``
* [``JWT - JSON Web Token``](https://jwt.io/) - Sử dụng để quản lý việc ``xác thực`` người dùng hệ thống
# Project Structure
```bash
├───frontend
│   ├───app
│   │   ├───common
│   │   ├───config
│   │   └───modules
│   ├───i18n
│   └───static
├───java
│   └───com
│       └───fps
│           └───mpits
│               ├───configuration
│               │   ├───database
│               │   ├───security
│               │   │   └───authentication
│               │   │       └───jwt
│               │   └───swagger
│               ├───exception
│               ├───logging
│               ├───modules
│               │   ├───app
│               │   │   ├───dto
│               │   │   ├───entity
│               │   │   ├───mapper
│               │   │   ├───repo
│               │   │   └───service
│               │   └───auth
│               │       ├───dto
│               │       ├───entity
│               │       ├───mapper
│               │       ├───repo
│               │       └───service
│               └───web
│                   └───controller
│                       ├───rest
│                       ├───util
│                       └───view
└───resources
    ├───static
    └───templates
```
* ``java``: thư mục gốc chứa mã nguồn lớp back-end
   * ``com.fps.mpits``: root package 
      * ``configuration``: package xử lý phần cấu hình chung
         * ``database``: code cấu hình cho Database
         * ``security``: code cấu hình bảo mật hệ thống
      * ``exception``: code các exception
      * ``logging``:
      * ``modules``: code logic xử lý từng chức năng
         * ``module1``: chức năng 1
            * ``dto``:
            * ``entity``:
            * ``mapper``:
            * ``repository``:
            * ``service``:
      * ``util``: code các utilities cho hệ thống
      * ``web``: code xử lý cho tầng view (REST/MVC controller)
* ``resources``:
# Code Example
## Sequence Diagram
```seq
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```
## Controller Layer
## Service Layer
## Repository Layer
## ``Mapper``
>**Note**:Sử dụng MapStruct để ``mapping value`` từ `entity` sang `dto` và `ngược lại`
```java
package com.fps.mpits.modules.auth.mapper;

import com.fps.mpits.modules.auth.entity.Role;
import com.fps.mpits.modules.auth.entity.RoleCategory;
import com.fps.mpits.modules.auth.entity.User;
import com.fps.mpits.modules.auth.dto.UserDTO;
import com.fps.mpits.modules.auth.repo.IRoleRepository;
import com.google.common.base.Enums;
import org.mapstruct.*;
import org.mapstruct.factory.Mappers;
import java.util.Optional;
import java.util.Set;
import java.util.stream.Collectors;

@Mapper
public interface UserMapper {
    static UserMapper MAPPER = Mappers.getMapper(UserMapper.class);

    @Mappings({
            @Mapping(source = "dto", target = "roles",qualifiedByName = {"MapStringToEnum"})
    })
    public User map(UserDTO dto, @Context IRoleRepository roleRepository);

    @Mapping(source = "user", target = "roles", qualifiedByName = "MapEnumToString")
    public UserDTO map(User user);

    @Named("MapStringToEnum")
    default Set<Role> transformStringToEnumRole(UserDTO dto, @Context IRoleRepository roleRepository){
        return dto.getRoles().stream().map(role->roleRepository.findByName(Enums.getIfPresent(RoleCategory.class,role).orNull()))
                .filter(Optional::isPresent).map(Optional::get).collect(Collectors.toSet());
    }
    @Named("MapEnumToString")
    default Set<String> transformEnumToStringRole(User user){
        return user.getRoles().stream().map(role->role.getName().name()).collect(Collectors.toSet());
    }
}
```
# API Docs
Sử dụng ``SwaggerUI``(https://swagger.io) để generate API Docs

Demo [``API Docs``](http://10.15.68.50:8082)
