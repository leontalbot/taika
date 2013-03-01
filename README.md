# Taika

A Clojure wrapper around the Firebase REST API.

**Note that this is not a Firebase client in Clojure, it's just the REST API**

## Usage

Taika tries to stay close to the wording used in the [Firebase REST Documentation](https://www.firebase.com/docs/rest-api.html). Make sure that either 1.) you've read the security documentation and have configured things appropriately, or 2.) have read/write access turned on for everything while testing. Security will likely be the biggest pain while getting started.

Add Taika to you project

    (require '[com.zenboxapp/taika :as taika])

Create a token generator and an auth token:

    (def user-auth-token
      (let [token-generator (taika/token-generator "SECRET-KEY")
            auth-data {:username "taika" :team_id 100}
            admin? false]
        (taika/create-token token-generator auth-data admin?)))

Create a new entry in the Firebase database (note we're using 10 as the customer's id/handle here):

    (taika/write! "db-name" "/customers" {10 {:name "Samuel Calans"}} user-auth-token)
    ; => {"10" {"name" "Samuel Calans"}}

The user-auth-token is optional, and only needed if your security rules require it, for example with reading data:

    (taika/read "db-name" "/customers/10")
    ; {"name" "Samuel Calans"}
 
 Update (merge) a given entry:
 
     (taika/update! "db-name" "/customers" {10 {:name "Samuel Hayes" :area "SF"}} user-auth-token)
     ; => {"10" {"name" "Samuel Hayes" :area "SF"}} 
     
 Destructively update (replace) an entry:
 
     (taika/write! "db-name" "/customers" {10 {:name "Noah Maranchi"}} user-auth-token)
     ; => {"10" {:name "Noah Maranchi"}}
 
Destroy data:

    (taika/destroy! "db-name" "/customers/10" user-auth-token)
    ; => nil

Finally, you can update the security rules from Taika as well. Given a Clojure map, Taika will replace the **ENTIRE** rule document with it. This method requires your secret key as well. Be careful with this!

    (taike/update-rules! "db-name" "SECRET-KEY" {:rules {:customers
                                                          {:write true
                                                           :read true}}})

## License

Copyright © 2013 Bushido Inc

Distributed under the Eclipse Public License, the same as Clojure.