---
title: OpenGL 1.1
---

#cs #lib #graphics

> [!info]
> Function families are grouped together.

```c
// OpenGL 1.1 in OPENGL32.DLL

glAccum( GLenum, GLfloat );
glAlphaFunc( GLenum, GLclampf );
glAreTexturesResident( GLsizei, const GLuint *, GLboolean * );
glArrayElement( GLint );
glBegin( GLenum );
glBindTexture( GLenum, GLuint );
glBitmap( GLsizei, GLsizei, GLfloat, GLfloat, GLfloat, GLfloat, const GLubyte * );
glBlendFunc( GLenum, GLenum );
glCallList( GLuint );
glCallLists( GLsizei, GLenum, const GLvoid * );
glClear( GLbitfield );
glClearAccum( GLfloat, GLfloat, GLfloat, GLfloat );
glClearColor( GLclampf, GLclampf, GLclampf, GLclampf );
glClearDepth( GLclampd );
glClearIndex( GLfloat );
glClearStencil( GLint );
glClipPlane( GLenum, const GLdouble * );
glColor*();
glColorMask( GLboolean, GLboolean, GLboolean, GLboolean );
glColorMaterial( GLenum, GLenum );
glColorPointer( GLint, GLenum, GLsizei, const GLvoid * );
glCopyPixels( GLint, GLint, GLsizei, GLsizei, GLenum );
glCopyTexImage*();
glCopyTexSubImage*();
glCullFace( GLenum );
glDeleteLists( GLuint, GLsizei );
glDeleteTextures( GLsizei, const GLuint * );
glDepthFunc( GLenum );
glDepthMask( GLboolean );
glDepthRange( GLclampd, GLclampd );
glDisable( GLenum );
glDisableClientState( GLenum );
glDrawArrays( GLenum, GLint, GLsizei );
glDrawBuffer( GLenum );
glDrawElements( GLenum, GLsizei, GLenum, const GLvoid * );
glDrawPixels( GLsizei, GLsizei, GLenum, GLenum, const GLvoid * );
glEdgeFlag*();
glEnable( GLenum );
glEnableClientState( GLenum );
glEnd( void );
glEndList( void );
glEvalCoord1d( GLdouble );
glEvalCoord*();
glEvalMesh*();
glEvalPoint*();
glFeedbackBuffer( GLsizei, GLenum, GLfloat * );
glFinish( void );
glFlush( void );
glFog*();
glFrontFace( GLenum );
glFrustum( GLdouble, GLdouble, GLdouble, GLdouble, GLdouble, GLdouble );
glGenLists( GLsizei );
glGenTextures( GLsizei, GLuint * );
glGetBooleanv( GLenum, GLboolean * );
glGetClipPlane( GLenum, GLdouble * );
glGetDoublev( GLenum, GLdouble * );
glGetError( void );
glGetFloatv( GLenum, GLfloat * );
glGetIntegerv( GLenum, GLint * );
glGetLight*();
glGetMap*();
glGetMaterial*();
glGetPixelMap*();
glGetPointerv( GLenum, GLvoid ** );
glGetPolygonStipple( GLubyte * );
glGetString( GLenum );
glGetTexEnv*();
glGetTexGen*();
glGetTexImage( GLenum, GLint, GLenum, GLenum, GLvoid * );
glGetTexLevelParameter*();
glGetTexParameter*();
glHint( GLenum, GLenum );
glIndexMask( GLuint );
glIndex*();
glInitNames( void );
glInterleavedArrays( GLenum, GLsizei, const GLvoid * );
glIsEnabled( GLenum );
glIsList( GLuint );
glIsTexture( GLuint );
glLightModel*();
glLight*();
glLineStipple( GLint, GLushort );
glLineWidth( GLfloat );
glListBase( GLuint );
glLoadIdentity( void );
glLoadMatrix*();
glLoadName( GLuint );
glLogicOp( GLenum );
glMap*();
glMapGrid*();
glMaterial*();
glMatrixMode( GLenum );
glMultMatrix*();
glNewList( GLuint, GLenum );
glNormal*();
glOrtho( GLdouble, GLdouble, GLdouble, GLdouble, GLdouble, GLdouble );
glPassThrough( GLfloat );
glPixelMap*();
glPixelStore*();
glPixelTransfer*();
glPixelZoom( GLfloat, GLfloat );
glPointSize( GLfloat );
glPolygonMode( GLenum, GLenum );
glPolygonOffset( GLfloat, GLfloat );
glPolygonStipple( const GLubyte * );
glPopAttrib( void );
glPopClientAttrib( void );
glPopMatrix( void );
glPopName( void );
glPrioritizeTextures( GLsizei, const GLuint *, const GLclampf * );
glPushAttrib( GLbitfield );
glPushClientAttrib( GLbitfield );
glPushMatrix( void );
glPushName( GLuint );
glRasterPos*();
glReadBuffer( GLenum );
glReadPixels( GLint, GLint, GLsizei, GLsizei, GLenum, GLenum, GLvoid * );
glRectd( GLdouble, GLdouble, GLdouble, GLdouble );
glRect*();
glRenderMode( GLenum );
glRotate*();
glScale*();
glScissor( GLint, GLint, GLsizei, GLsizei );
glSelectBuffer( GLsizei, GLuint * );
glShadeModel( GLenum );
glStencilFunc( GLenum, GLint, GLuint );
glStencilMask( GLuint );
glStencilOp( GLenum, GLenum, GLenum );
glTexCoord*();
glTexEnv*();
glTexGen*();
glTexImage*();
glTexParameter*();
glTexSubImage*();
glTranslate*();
glVertex*();
glViewport( GLint, GLint, GLsizei, GLsizei );
```

## Resources

- [MSDN OpenGL Docs](https://learn.microsoft.com/en-us/windows/win32/opengl/gl-functions)