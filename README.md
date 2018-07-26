# stateless-web-app
Build Web Application Stateless
# Dependencies
![](https://img.shields.io/badge/java_8-✓-blue.svg)
![](https://img.shields.io/badge/maven_3+-✓-blue.svg)
![](https://img.shields.io/badge/spring_boot-✓-blue.svg)
![](https://img.shields.io/badge/swagger_2-✓-blue.svg)
![](https://img.shields.io/badge/oracle-✓-blue.svg)
![](https://img.shields.io/badge/mapstruct-✓-blue.svg)
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
