node {
	properties([
		pipelineTriggers([
			[$class: 'GenericTrigger',
				genericVariables: [
					[key: 'BODY', value: '$'],
				],
	//			genericRequestVariables: [
	//				[key: 'requestWithNumber', regexpFilter: '[^0-9]'],
	//				[key: 'requestWithString', regexpFilter: '']
	//			],
	//			genericHeaderVariables: [
	//				[key: 'headerWithNumber', regexpFilter: '[^0-9]'],
	//				[key: 'headerWithString', regexpFilter: '']
	//			],


	//			token: 'abc123',

				printContributedVariables: true,
				printPostContent: true,

				silentResponse: false,

	//			regexpFilterText: '$ref',
	//			regexpFilterExpression: 'refs/heads/' + BRANCH_NAME
			]
		])
	])

	stage("build") {
	sh '''
		echo Variables from shell:
		echo ref $BODY
	'''
	}
}