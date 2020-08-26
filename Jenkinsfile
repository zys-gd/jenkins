node {
	properties([

		[$class: 'GenericTrigger',
			genericVariables: [
				[expressionType: 'JSONPath', key: 'BODY', value: '$'],
			],





			printContributedVariables: true,
			printPostContent: true,

			silentResponse: false,

			regexpFilterText: '',
			regexpFilterExpression: ''
		]

	])

	stage("build") {
	sh '''
		echo Variables from shell:
		echo ref $BODY
	'''
	}
}