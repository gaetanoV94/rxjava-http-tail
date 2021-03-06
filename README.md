# rxjava-http-tail [![unlicense](https://img.shields.io/badge/un-license-green.svg?style=flat)](http://unlicense.org)

A [RxJava](https://github.com/Netflix/RxJava/wiki)-based port of [Net::HTTP::FollowTail](https://github.com/broquaint/net-http-follow_tail) to the JVM-land.

This allows you to follow logs like `tail -f` through HTTP.

## Installation

rxjava-http-tail is pushed to [Clojars](https://clojars.org).

So if you're using Leiningen, just add this dependency:

```clojure
[rxjava-http-tail "0.1.2"]
```

With Gradle:

```groovy
repositories {
  maven {
    url "http://clojars.org/repo"
  }
}

dependencies {
  runtime group: 'rxjava-http-tail', name: 'rxjava-http-tail', version: '0.1.2'
}
```

And Maven:

```xml
<repository>
  <id>clojars.org</id>
  <url>http://clojars.org/repo</url>
</repository>
```

```xml
<dependency>
  <groupId>rxjava-http-tail</groupId>
  <artifactId>rxjava-http-tail</artifactId>
  <version>0.1.2</version>
</dependency>
```

## Usage

It can be used from any language on the JVM, but here's an example in Clojure:

```clojure
(ns example
  (:import rx.HttpTail))

(defn read-offset []
  (Long/parseLong (slurp "offset.txt")))

(defn write-offset [o]
  (spit "offset.txt" o))

(defn on-result [result]
  (let [body (.getBody result)] ; the body is an instance of java.io.InputStream
    (write-offset (.getOffset result))
    (prn (slurp body))
    (.close body)))

(-> (HttpTail. "http://crawl.akrasiac.org/logfile-git" (read-offset) 15000)
    .createObservable
    (.subscribe on-result))
```

```shell
$ echo -n "59690466" > offset.txt
$ lein run -m example
```

## Contributing

Please feel free to submit pull requests!
Bugfixes and simple non-breaking improvements will be accepted without any questions :-)

By participating in this project you agree to follow the [Contributor Code of Conduct](http://contributor-covenant.org/version/1/1/0/).

## License

This is free and unencumbered software released into the public domain.  
For more information, please refer to the `UNLICENSE` file or [unlicense.org](http://unlicense.org).
