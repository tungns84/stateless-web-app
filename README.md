# New Web App

Phát triển ứng dụng web sử dụng ReactJS và RESTFul Web Services.

# Công nghệ sử dụng
* [``Java 8``](https://www.java.com) - Ngôn ngữ phát triển lớp back-end
* [``Maven 3+``](https://maven.apache.org/) - Quản lý các dependency sử dụng trong dự án
* [``Spring Framework``](https://spring.io/) - Sử dụng phát triển lớp back-end
    * Spring Boot
    * Spring Web
    * Spring Security
    * Spring Data JPA
    * 
* [``ReactJS``](https://reactjs.org/) - Sử dụng phát triển lớp front-end
* [``JWT - JSON Web Token``](https://jwt.io/) - Sử dụng để quản lý việc xác thực người dùng hệ thống
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
         * ``database``: xử lý cấu hình cho Database
         * ``security``: xử lý cấu hình bảo mật
      * ``exception``: xử lý các exception
      * ``logging``:
      * ``modules``: viết logic xử lý từng chức năng
      * ``util``: viết các utilities cho hệ thống
      * ``web``: xử lý tại tầng view(REST/MVC controller)

# Mapper
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
