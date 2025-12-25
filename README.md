# Design Patterns Utilisés dans l'Application

Ce document décrit tous les design patterns implémentés dans l'application Inventory Management System et leur localisation exacte dans le code.

---

## 1. Repository Pattern

**Description** : Abstraction de la couche d'accès aux données, permettant de séparer la logique métier de la persistance.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/repositories/CategoryRepository.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/repositories/ProductRepository.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/repositories/SupplierRepository.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/repositories/TransactionRepository.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/repositories/UserRepository.java`

**Exemple** :
```java
public interface CategoryRepository extends JpaRepository<Category, Long> {
}
```

**Avantages** :
- Séparation des préoccupations
- Facilite les tests unitaires avec des mocks
- Abstraction de la couche de persistance

---

## 2. Service Layer Pattern

**Description** : Pattern qui sépare la logique métier de la couche de présentation en utilisant des interfaces et leurs implémentations.

**Localisation** :
- **Interfaces** :
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/CategoryService.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/ProductService.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/SupplierService.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/TransactionService.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/UserService.java`

- **Implémentations** :
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/CategoryServiceImpl.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/ProductServiceImpl.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/SupplierServiceImpl.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/TransactionServiceImpl.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/UserServiceImpl.java`

**Exemple** :
```java
public interface CategoryService {
    Response createCategory(CategoryDTO categoryDTO);
    Response getAllCategories();
    // ...
}

@Service
public class CategoryServiceImpl implements CategoryService {
    // Implémentation
}
```

**Avantages** :
- Encapsulation de la logique métier
- Facilite les tests et la maintenance
- Permet de changer l'implémentation sans affecter les contrôleurs

---

## 3. DTO (Data Transfer Object) Pattern

**Description** : Objets utilisés pour transférer des données entre les couches de l'application, évitant d'exposer directement les entités.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/CategoryDTO.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/ProductDTO.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/SupplierDTO.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/TransactionDTO.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/UserDTO.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/LoginRequest.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/RegisterRequest.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/TransactionRequest.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/Response.java`

**Exemple** :
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CategoryDTO {
    private Long id;
    private String name;
    private List<ProductDTO> products;
}
```

**Avantages** :
- Contrôle des données exposées via l'API
- Évite l'exposition de la structure interne des entités
- Facilite la versioning de l'API

---

## 4. Builder Pattern

**Description** : Pattern de construction d'objets complexes de manière fluide et lisible.

**Localisation** :
- Tous les modèles utilisent `@Builder` de Lombok :
  - `backend/src/main/java/com/company/InventoryMgtSystem/models/Category.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/models/Product.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/models/Supplier.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/models/Transaction.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/models/User.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/dtos/Response.java`

**Exemple** :
```java
@Builder
@Data
public class Product {
    private Long id;
    private String name;
    // ...
}

// Utilisation
Product product = Product.builder()
    .name("Laptop")
    .price(new BigDecimal("999.99"))
    .stockQuantity(10)
    .build();
```

**Avantages** :
- Construction d'objets complexes de manière lisible
- Paramètres optionnels faciles à gérer
- Code plus maintenable

---

## 5. Dependency Injection Pattern

**Description** : Pattern d'inversion de contrôle où les dépendances sont injectées plutôt que créées directement.

**Localisation** :
- Utilisé partout dans l'application via :
  - `@RequiredArgsConstructor` (Lombok) - Injection par constructeur
  - `@Autowired` (Spring) - Injection automatique
  - `@MockBean` dans les tests

**Exemple** :
```java
@Service
@RequiredArgsConstructor
@Slf4j
public class CategoryServiceImpl implements CategoryService {
    private final CategoryRepository categoryRepository;
    private final ModelMapper modelMapper;
    // Les dépendances sont injectées automatiquement
}
```

**Avantages** :
- Couplage faible entre les composants
- Facilite les tests unitaires
- Gestion du cycle de vie par le conteneur Spring

---

## 6. Factory Pattern

**Description** : Pattern de création d'objets via une méthode factory.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/config/ModelMapperConfig.java`

**Exemple** :
```java
@Configuration
public class ModelMapperConfig {
    @Bean
    public ModelMapper modelMapper() {
        ModelMapper modelMapper = new ModelMapper();
        modelMapper.getConfiguration()
                .setFieldMatchingEnabled(true)
                .setFieldAccessLevel(Configuration.AccessLevel.PRIVATE)
                .setMatchingStrategy(MatchingStrategies.STANDARD);
        return modelMapper;
    }
}
```

**Avantages** :
- Centralisation de la création d'objets complexes
- Configuration centralisée
- Réutilisabilité

---

## 7. Adapter Pattern

**Description** : Pattern permettant d'adapter une interface à une autre.

**Localisation** :
- Utilisation de `ModelMapper` pour convertir entre Entity et DTO :
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/CategoryServiceImpl.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/ProductServiceImpl.java`
  - Tous les services utilisent ModelMapper pour adapter Entity ↔ DTO

**Exemple** :
```java
Category categoryToSave = modelMapper.map(categoryDTO, Category.class);
CategoryDTO categoryDTO = modelMapper.map(category, CategoryDTO.class);
```

**Avantages** :
- Conversion automatique entre différents formats
- Réduction du code boilerplate
- Séparation claire entre entités et DTOs

---

## 8. Strategy Pattern

**Description** : Pattern permettant de définir une famille d'algorithmes et de les rendre interchangeables.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/specification/TransactionFilter.java`

**Exemple** :
```java
public class TransactionFilter {
    public static Specification<Transaction> byFilter(String searchValue) {
        return (root, query, criteriaBuilder) -> {
            // Stratégie de filtrage dynamique
            // ...
        };
    }

    public static Specification<Transaction> byMonthAndYear(int month, int year) {
        return (root, query, criteriaBuilder) -> {
            // Autre stratégie de filtrage
            // ...
        };
    }
}
```

**Utilisation** :
```java
Specification<Transaction> spec = TransactionFilter.byFilter(filter);
Page<Transaction> transactionPage = transactionRepository.findAll(spec, pageable);
```

**Avantages** :
- Algorithmes interchangeables
- Extensibilité facile
- Séparation des stratégies de filtrage

---

## 9. Filter Pattern (Chain of Responsibility)

**Description** : Pattern permettant de traiter une requête à travers une chaîne de filtres.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/security/AuthFilter.java`
- `backend/src/main/java/com/company/InventoryMgtSystem/security/SecurityConfig.java`

**Exemple** :
```java
@Component
public class AuthFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                   HttpServletResponse response, 
                                   FilterChain filterChain) {
        // Traitement de l'authentification JWT
        // ...
        filterChain.doFilter(request, response);
    }
}
```

**Configuration** :
```java
.addFilterBefore(authFilter, UsernamePasswordAuthenticationFilter.class);
```

**Avantages** :
- Traitement séquentiel des requêtes
- Modularité des filtres
- Facilite l'ajout de nouveaux filtres

---

## 10. Facade Pattern

**Description** : Pattern fournissant une interface simplifiée à un sous-système complexe.

**Localisation** :
- Les services agissent comme des facades pour les contrôleurs :
  - `backend/src/main/java/com/company/InventoryMgtSystem/services/impl/TransactionServiceImpl.java`
  - Tous les services simplifient l'accès aux repositories et à la logique métier

**Exemple** :
```java
// Le contrôleur utilise la facade (service)
@RestController
public class CategoryController {
    private final CategoryService categoryService;
    
    @PostMapping("/add")
    public ResponseEntity<Response> createCategory(@RequestBody CategoryDTO categoryDTO) {
        return ResponseEntity.ok(categoryService.createCategory(categoryDTO));
    }
}
```

**Avantages** :
- Interface simplifiée pour les contrôleurs
- Masque la complexité de la logique métier
- Facilite la maintenance

---

## 11. Singleton Pattern

**Description** : Pattern garantissant qu'une classe n'a qu'une seule instance.

**Localisation** :
- Implémenté par Spring via `@Bean` et `@Component` :
  - Tous les beans Spring sont des singletons par défaut
  - `backend/src/main/java/com/company/InventoryMgtSystem/config/ModelMapperConfig.java`
  - `backend/src/main/java/com/company/InventoryMgtSystem/security/SecurityConfig.java`

**Exemple** :
```java
@Configuration
public class ModelMapperConfig {
    @Bean  // Singleton par défaut dans Spring
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }
}
```

**Avantages** :
- Une seule instance partagée
- Économie de ressources
- Gestion centralisée

---

## 12. Template Method Pattern

**Description** : Pattern définissant le squelette d'un algorithme dans une méthode, laissant certaines étapes aux sous-classes.

**Localisation** :
- Implémenté par Spring Data JPA :
  - `JpaRepository` fournit des méthodes template (save, findById, delete, etc.)
  - Tous les repositories héritent de ces méthodes template

**Exemple** :
```java
public interface CategoryRepository extends JpaRepository<Category, Long> {
    // Hérite automatiquement de :
    // - save(), findById(), findAll(), delete(), etc.
}
```

**Avantages** :
- Réduction du code boilerplate
- Méthodes communes réutilisables
- Extensibilité facile

---

## 13. Exception Handler Pattern (Global Exception Handling)

**Description** : Pattern centralisé pour la gestion des exceptions dans l'application.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/exceptions/GlobalExceptionHandler.java`

**Exemple** :
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<Response> handleNotFoundException(NotFoundException ex) {
        Response response = Response.builder()
                .status(HttpStatus.NOT_FOUND.value())
                .message(ex.getMessage())
                .build();
        return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
    }
}
```

**Avantages** :
- Gestion centralisée des erreurs
- Réponses d'erreur cohérentes
- Séparation des préoccupations

---

## 14. Specification Pattern

**Description** : Pattern permettant de construire des requêtes complexes de manière déclarative.

**Localisation** :
- `backend/src/main/java/com/company/InventoryMgtSystem/specification/TransactionFilter.java`

**Exemple** :
```java
public static Specification<Transaction> byFilter(String searchValue) {
    return (root, query, criteriaBuilder) -> {
        List<Predicate> predicates = new ArrayList<>();
        // Construction dynamique de prédicats
        // ...
        return criteriaBuilder.or(predicates.toArray(new Predicate[0]));
    };
}
```

**Avantages** :
- Requêtes dynamiques et réutilisables
- Code déclaratif et lisible
- Facilite les requêtes complexes

---

## Résumé des Design Patterns

| Pattern | Localisation Principale | Objectif |
|--------|------------------------|----------|
| Repository | `repositories/` | Abstraction de l'accès aux données |
| Service Layer | `services/` | Séparation de la logique métier |
| DTO | `dtos/` | Transfert de données |
| Builder | `models/`, `dtos/Response.java` | Construction d'objets |
| Dependency Injection | Partout (Spring) | Inversion de contrôle |
| Factory | `config/ModelMapperConfig.java` | Création d'objets |
| Adapter | Services (ModelMapper) | Conversion Entity ↔ DTO |
| Strategy | `specification/TransactionFilter.java` | Algorithmes interchangeables |
| Filter | `security/AuthFilter.java` | Chaîne de filtres |
| Facade | Services | Interface simplifiée |
| Singleton | Beans Spring | Instance unique |
| Template Method | Spring Data JPA | Méthodes template |
| Exception Handler | `exceptions/GlobalExceptionHandler.java` | Gestion centralisée |
| Specification | `specification/TransactionFilter.java` | Requêtes dynamiques |

---

## Conclusion

Ces design patterns contribuent à :
- **Maintenabilité** : Code organisé et modulaire
- **Testabilité** : Composants facilement testables
- **Extensibilité** : Facile d'ajouter de nouvelles fonctionnalités
- **Séparation des préoccupations** : Chaque couche a sa responsabilité
- **Réutilisabilité** : Composants réutilisables dans différents contextes

