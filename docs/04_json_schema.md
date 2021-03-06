# JSON Schemas

```clj
(require '[spec-tools.json-schema :as json-schema])
```

Utility to transform Specs into JSON Schemas.

```clj
(require '[spec-tools.json-schema :as json-schema])
(require '[clojure.spec.alpha :as s])

(s/def ::id string?)
(s/def ::name string?)
(s/def ::street string?)
(s/def ::city #{:tre :hki})
(s/def ::address (s/keys :req-un [::street ::city]))
(s/def ::user (s/keys :req-un [::id ::name ::address]))

(json-schema/transform ::user)
;{:type "object"
; :properties {"id" {:type "string"}
;              "name" {:type "string"}
;              "address" {:type "object"
;                         :properties {"street" {:type "string"}
;                                      "city" {:enum [:tre :hki]}}
;                         :required ["street" "city"]
;                         :title "user/address"}}
; :required ["id" "name" "address"]
; :title "user/user"}
```

## Annotated Specs

Specs can be annotated to populate the JSON Schemas.
 
* `:name` is copied into `:title` 
* `:description` is used as-is
* all keys with namespace `json-schema` are used, without the namespace

```clj
(require '[spec-tools.core :as st])

(json-schema/transform
  (st/spec
    {:spec integer?
     :name "integer"
     :description "it's an int"
     :json-schema/default 42}))
;{:type "integer"
; :title "integer"
; :description "it's an int"
; :default 42}
```
