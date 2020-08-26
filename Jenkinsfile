node
{
	GenericTrigger {
		genericVariables {
			genericVariable {
				key("BODY")
				value("\$")
			}
		}
	}

	stage("build") {
    sh '''
        echo body $BODY
    '''
    }
}
