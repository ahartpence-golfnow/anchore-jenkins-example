hartpenceTest {
    buildCloudId = 'build-cloud'
    jenkins = [
        approversId: 'authorization-approver-list-teeleader'
    ]
    git = [
        buildableBranches: [ 'GNCBE-\\d+-STAGE' ],
        masterBranch: 'master'
    ]
    newrelic = [
        baseUrlId: 'golfnow-newrelic-base-url',
        licenseKeyId: 'golfnow-newrelic-api-key',
        applicationId: '79096478'
    ]
    docker = [
        keyFileId: 'gcr-kenna-experimental',
        repository: 'gcr.io/kenna-experimental-datacenter/hartpence-test',
        filePath: 'Dockerfile',
        buildArgs: [:],
        tag: 'latest'
    ]
    tests = [
        [
            enabled: false,
            commands: "NODE_ENV=test npm install && NODE_ENV=test NODE_ENV=test TEST_HOST=127.0.0.1 TEST_PORT=9000 REDIS_TESTHOST=127.0.0.1 REDIS_HOST=127.0.0.1 ./node_modules/lab/bin/lab test/*.jsx",
            backends: [
                [
                    "image": "redis:3.0.7",
                    "ports": [ 6379 ]
                ]
            ]
        ]
    ]
    kubernetes = [
        staging: [
            substitutions: [
                '@{namespace}': 'default',
                '"@{replicas}"': '2'
            ],
            configuration: [
                namespace: 'default',
                credentials: 'kubeconfig-kenna-staging-gke'
            ],
            apply: [
                [
                    files: [
                        'k8s/qa/*.json'
                    ]
                ]
            ]
        ],
        production: [
            substitutions: [
                '@{namespace}': 'default',
                '"@{replicas}"': '4'
            ],
            configuration: [
                namespace: 'default',
                credentials: [
                    source: 'kubeconfig-kenna-staging-gke',
                    target: 'kubeconfig-b2b-001-us-east1'
                ]
            ],
            apply: [
                [
                    files: [
                        'k8s/prod/cm.json',
                        'k8s/prod/ing.json',
                        'k8s/prod/ing-5-1.json',
                        'k8s/prod/svc.json',
                        'k8s/prod/svc-5-1.json'
                    ]
                ]
            ],
            mappings: [
                [
                    map: [
                        sourceDeploymentName: 'phx-ui-be-5-1',
                        deploymentFile: 'k8s/prod/deploy.json'
                    ]
                ]
            ]
        ]
    ]
}
