# 1. LogStash
로그 스태시는 데이터를 수집, 가공하여 다른 저장소(여기서는 엘라스틱 서치)에 데이터를 전송하는 **데이터 처리 파이프라인** 이다.
기본 기능 외에도 다양한 플러그인을 지원하여, LogStash의 기능이나 역할 범위를 확장할 수 있다.

로그 스태시의 파이프라인은 입력(Input), 필터(Filter), 출력(Output)으로 기본 구성되며, 각자의 역할을 명확히 한다. 각 파이프라인에서의 단계는 여러 플러그인의 조합으로 구성되며,
순차적으로 실행된다.

예를 들어, 입력단계에서 파일 플러그인을 통해 파일을 로드하고, 필터 단계에 로드된 데이터를 JSON으로 파싱하여, 출력단계에서 엘라스틱에 저장하는 식의 원리처럼 말이다.

최근에는 ELK Stack에 대한 관심도가 높아지면서, 여러 상황을 고려한 아키텍쳐에 대해 고안되고있는데, 이러한 여러 아키텍쳐를 효과적으로 구현하도록 돕는 비츠(Beats) 플랫폼도 생겨나고
이밖에 다양한 플러그인들이 추가 개발되고 있다.

이 ELK Stack을 실제 응용하여 원하는 아키텍쳐를 구현 즉, 적절한 상황에 다양한 플러그인을 조합하여 원하는 형태로 사용하는 역할은 우리의 몫이다.

## 1.1 로그스태시와 파이프라인
파이프라인은 로그스태시 홈 디렉터리의 config 디렉터리 안에 설정파일을 구성하여 작성한다. 해당 설정 파일은 Ruby 언어를 사용한다.
해당 설정파일을 통해, 값 설정 외에도 다양한 조건을 작성할 수 있다.

로그스태시는 파이프라인 설정 파일을 작성하지 않은 상태로는 구동될 수 없다.

> ERROR: Pipelines YAML file is empty. Location : $LOGSTASH_HOME/config/pipelines.yml

로그 스태시 홈디렉터리를 지정하고, 해당 디렉터리 내에 pipeline 디렉터리를 생성한다.
```shell
mkdir $LOGSTASH_HOME/pipeline
```

생성된 파이프라인 디렉터리에 1번 파이프라인을 작성한다.

```shell
vi $LOGSTASH_HOME/pipeline/first-pipeline.conf
```

```shell
input {
  stdin {}
}
filter {
  
}
output{
  stdout {}
}
```
앞서 언급한 바와 같이, 로그스태시 파이프라인은 루비 언어 기반으로 작성한다.

기본 파이프라인 작성은 완료되었고, 위의 파이프라인과 같이 키보드 입력을 받은(input단계)내용을
필터를 걸쳐 출력(output단계) 해주는 간단한 파이프라인이다. 이를 구동해보도록 하자!

우선 파이프라인 설정을 저장하고 해당 파이프라인 설정 파일 위치를 로그스태시가 인식할 수 있도록 설정해야한다.
설정파일 경로는 config/logstash.yml 이나 config/pipeline.yml 설정 파일을 통해 작성하거나 또는 로그스태시 실행 시 -f 옵션으로 파일을 직접 지정할 수 있다.


 * **config/logstash.yml 파일에 파이프라인 설정 파일 디렉터리 지정하기**

이 파일은 로그스태시를 위한 다양한 설정을 지정할 수 있는데, 기본 제공하기 위한 것 외에는 모두 주석처리 되어있다. 따라서, 제공받고자하는 대상의 주석을 해제하고 설정해야한다.
우리는 로그스태시 구동을 위해 작성한 파이프라인의 설정 정보 경로를 지정할 것이다.

주석 처리된 **path.config** 항목을 찾아 주석을 해제하고, 파이프라인 경로를 작성한다.

```shell
path.config: '${LOGSTASH_HOME}/pipeline'
```

설정 완료 후, 로그 스태시 홈 디렉터리의 bin 디렉터리로 이동 후, logstash 쉘스크립트 파일을 실행하자.

```shell
root@1835c8716a82:~/elasticbooks/project/elasticStack/programs/logstash/bin# ./logstash
Sending Logstash logs to /root/elasticbooks/project/elasticStack/programs/logstash/logs which is now configured via log4j2.properties
[2021-06-02T07:08:05,885][WARN ][logstash.config.source.multilocal] Ignoring the 'pipelines.yml' file because modules or command line options are specified
[2021-06-02T07:08:06,874][INFO ][logstash.runner          ] Starting Logstash {"logstash.version"=>"6.4.0"}
[2021-06-02T07:08:09,435][INFO ][logstash.pipeline        ] Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>4, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50}
[2021-06-02T07:08:09,626][INFO ][logstash.pipeline        ] Pipeline started successfully {:pipeline_id=>"main", :thread=>"#<Thread:0x325e64b0 run>"}
The stdin plugin is now waiting for input:
[2021-06-02T07:08:09,731][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2021-06-02T07:08:10,135][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
```

다음과 같이 출력되었다면, 파이프라인이 동작된 것이다.

작성한 파이프라인대로 input단계 수행을 위해 키보드 입력을 수행하고, output단계에 의해 입력된 값을 출력하는지 확인해보자

```shell
logstash pipeline
{
       "message" => "logstash pipeline",
    "@timestamp" => 2021-06-02T07:08:17.400Z,
          "host" => "1835c8716a82",
      "@version" => "1"
}
hello
{
       "message" => "hello",
    "@timestamp" => 2021-06-02T07:08:23.245Z,
          "host" => "1835c8716a82",
      "@version" => "1"
}
```

logstash pipeline 과 hello 라는 입력을 수행하였다. 메시지에 입력한 값이 잘 출력되었다.

* **-f 옵션을 사용한 파이프라인 동작**

이번에는 파이프라인을 실행하는 방법으로 logstash.yml에 작성하여 파이프라인을 실행하는 방법이 아닌, -f 옵션을 사용하여 특정 파이프라인 설정파일을 지정해 동작시켜보자.

```shell
logstash -f $LOGSTASH_HOME/pipeline/first-pipeline.conf
```

오타없이 잘 입력했다면 기존 동작했던 방법과 동일하게 로그스태시가 파이프라인 설정 파일을 대상으로 동작시킬 것이다.

### 1.1.1 입력 플러그인
본격적으로 입력 플러그인을 사용하여 입력 데이터를 받아보자.

**파일 플러그인**
파일 플러그인은 linux 명령에서 tail 처럼 특정 파일 대상으로 새롭게 작성되는 이벤트를 감지하고 수집하는 역할을 하는
플러그인이라고 생각하면 간단하다. 예제에서는 파일 플러그인을 통해, 수집된 데이터를 가공한 후 출력 플러그인으로 엘라스틱서치를 지정해 인덱싱해보자.

```shell
touch $LOGSTASH_HOME/input.txt
```

해당 파일을 저격하여 해당 파일에 새로운 이벤트가 발생할때마다 파이프라인이 동작하여 엘라스틱서치에 데이터를 인덱싱할 것이다.
이제 파이프라인을 작성해보자.

파일명은 file_plugin.conf로 지정해서 테스트하였다.
```shell
input {
  file {
    path => "${LOGSTASH_HOME}/input.txt" //1)path
    start_position => "beginning" //2)start_position
  }
}
filter {
  
}
output {
  stdout {}
}
```

1) path : 파일 플러그인을 사용하기 위해 필수로 입력되어져야하는 값이다. 어떤 파일을 대상으로 파일 플러그인을 동작시킬지에 대한 대상을 지정하는 값이다.
해당 패스는 파일로 한정할 수도 있지만, 특정 디렉터리 내 존재하는 모든파일을 대상으로 지정하기위해 '*' 와일드 카드를 작성할 수도 있다.

2) start_position : 파일을 읽을 위치를 지정한다. beginning 과 end 둘 중 하나를 택할 수 있는데, 말그대로 파일의 시작점부터 데이터를 수집하거나 파일의 마지막부터 데이터를 수집한다.
기본값은 end이다.
   
   
작성된 파이프라인을 이제 다시 구동시켜보자.

```shell
$LOGSTASH_HOME/bin/logstash -f $LOGSTASH_HOME/pipeline/file_plugin.conf
```

우리의 입력 플러그인 대상은 파일이다. input.txt에 echo 커맨드를 사용하여, 임의의 데이터를 삽입하자.

```shell
echo "I Love Elastic">> $LOGSTASH_HOME/input.txt
```
입력에 따라, 해당 파일에 새로운 이벤트가 발생하게 되었다. input단계에 있는 파이프라인도 이를 감지했는지 아래와 같이
입력된 데이터를 출력하였다(output 단계에 의해).

```shell
{
          "path" => "/root/elasticbooks/project/elasticStack/programs/logstash/input.txt",
          "host" => "1835c8716a82",
      "@version" => "1",
    "@timestamp" => 2021-06-02T08:06:16.010Z,
       "message" => "I Love Elastic"
}
```

* **sincedb**

로그스태시는 파일로부터 데이터를 수집할 때마다 파일의 어디까지를 읽었는지 책갈피와같은 정보를 가지고있다. 그래서, 로그스태시가 중간에
부득이한 이유로 혹은 관리자의 무언가의 이유로 중단되어도 다시 시작하면 입력이 멈춘 시점부터 다시 진행한다. 그 내용을 sincedb에 담고있는 것이다.

sincedb 파일의 경로는 지정하지 않으면 디폴트로 $LOGSTASH_HOME/data/plugins/inputs/file에 저장하고,
logstash.yml 파일을 통해 수동으로 sincedb 파일이 생성되는 경로를 지정할 수 있다.

그리고 이 파일은 숨김파일이여서, 확인하기 위해 **ls -a 경로**로 확인해야한다.

.sincedb 파일이 수집하고있는 정보
 * inode 번호 : 파일 식별자
 * 파일 시스템의 메이저 디바이스 번호 : 
 * 파일 시스템의 마이너 디바이스 번호 : 
 * 파일 내의 현재 바이트 오프셋 : 현재 어디까지 읽었었는지 기록한다 
 * 최근 수정한 시점의 타임스탬프 
 * 해당 파일의 경로