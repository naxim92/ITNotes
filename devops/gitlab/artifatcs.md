#gitlab #artifacts

- Jobs наследуют артефакты прошлых jobs. Это поведение можно поменять в [`dependencies`](https://docs.gitlab.com/ee/ci/yaml/#dependencies)
- Jobs исполняются на независимых раннерах параллельно