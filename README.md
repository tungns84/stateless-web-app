# New Web App
Build Web Application Stateless
# Technical Stack
* [Java 8](https://www.java.com) - The programming language used
* [Maven 3+](https://maven.apache.org/) - Dependency and build Management
* [Spring Framework](https://spring.io/) - Used to developing back-end layer
    * Spring Boot
    * Spring Web
    * Spring Security
    * Spring Data JPA
    * 
* [ReactJS](https://reactjs.org/) - Used to developing front-end layer
* [JWT](https://jwt.io/) - JSON Web Token. Used to manage Authorization process
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

Ví dụ về API Docs sử dụng SwaggerUI [API Docs](http://10.15.68.50:8082)
